# For-expressions and Monads

## Recap Functions and Pattern Matching

### Functions are objects

+ In scala, every type is the type of some class or trait. Function type is no exception
+ ```JBinding => String ```
is shorthand for
+ ```scala.Function1[JBinding, String]```
+ where ```scala.Function1``` is a train and ```JBinding, String``` are its type arguments

### Partial Functions

+ you can change a pattern match functino to a ```PartialFunction``` which automatically gives your function an ```isDefinedAt``` method. 

## Queries with For

+ For Expressions look a lot like SQL queries.
+ Find titles of books whose author's name is "Bird"

```scala

for (b <- books; a <- b.authors if a startsWith "Bird,")
yield b.title

```

+ Find the name of all authors who have written at least two books present in the db

```scala

{ for {
b1 <- books
b2 <- books
if b1 < b2 // we do this instead of b1 != b2 so that we don't get two pairs of each author
a1 <- b1.authors
a2 <- b2.authors
if a1 == a2
} yield a1 
}.distinct // we use distinct in addition to < since an author may have more than two books

```

## Translation of For

+ implementation of for-expressions. there is a systematic translation that maps for-exp's to combinations of higher-order functions.
+ for is closely related to  ```map```, ```flatMap``` and ```filter```
  + ```xs.map(x => f(x))``` == ```for (x <- xs) yield f(x)```
  + ```xs.flatMap(x => f(x))``` == ```for (x <- xs; y <- f(x)) yield y```
  + ```x.filter(x => p(x))``` == ```for (x <- xs if p(x)) yield x```
+ scala compiler translates ```for``` expressions to ```map```, ```flatMap``` and a lazy variant of ```filter```
  + lazy ```filter``` is ```withFilter```
+ these are the basis for Scala database connection frameworks ScalaQuery and Slick, similar ideas underly M$ LINQ

## Functional Random Generators

+ we build up generator for various types (booleans, pairs, etc) by defining an integer random number generator using a java random number generator built in function.
+ writing ```self => ``` in a superclass creates an alias to that classes ```this``` parameter so that you can call the superclasses ```this``` without invoking the subclasses ```this```
+ i.e:

```scala

trait Generator[+t] {
self =>   // alias for "this"

def generate: T

def map[S](f: T => S): Generator[S] = new Generator[S] {
  def generate = f(self.generate)
}

```

+ you can also just do name of function.this.f i.e. ```Generator.this.generate```

+ **culminates in explaining quickcheck and ScalaCheck, which use random number generators to ensure tests work for many conditions. you just specify how many times you want the tests to run.**


## Monads

+ a monad ```M``` is a parametric type ```M[T]``` with two operations, ```flatMap``` and ```unit```, that have to satisfy some laws
  + in the literature ```flatMap``` is called ```bind```
+ in scala, it's a trait as follows:

```scala

trait M[T] {
  def flatMap[U](f: T => M[U]): M[U]
}

def unit[T](x: T): M[T]

```

+ Examples of Monads:
  + ```List``` is a monad with ```unit(x) = List(x)```
  + ```Set``` is a monad with ```unit(x) = Set(x)```
  + ```Option``` is a monad with ```unit(x) = Some(x)```
  + ```Generator``` is a monad with ```unit(x) = single(x)```
+ ```flatMap``` is an operation on each of these types, whereas ```unit``` in scala is diff for each monad

### Monads and map

+ map is defined for every monad as combination of flatMap and unit

```scala

m.map(f) == m.flatMap(x => unit(f(x)))
         == m.flatMap(f andThen unit)

```

### Monad Laws

+ to qualify as a monad, a type has to satisfy three laws:
  + *Associativity:*
    + ```m.fatMap(f).flatMap(g) == m.flatMap(x => f(x).flatMap(g))```
  + *Left unit*
    + ```unit(x).flatMap(f) == f(x)```
  + *Right unit*
    + ``` m.flatMap(unit) == m ```

### Monoids

+ superset of Monad, but without ```bind```/```flatMap```
+ i.e. Integer is a Monoid becuase it follows associativity
  + ```x + (y + z) == (x + y) + z ```

## Try Type

+ ```Try``` resembles an ```Option``` but instead fo ```Some/None``` there is a ```Sucess``` case with a value and a ```Failure``` case that throws an exception.

```scala

abstract class Try[+T]
case class Successs[T](X: t)      extends Try[T]
case class Failure(ex: Exception) extends Try[Nothing]

```

+ ```Try``` is used to pass results of computations that can fail with an exception between threads and computers without intterupting computation

+ You can wrap an arbitrary computation in a ```Try```
+ e.g. ```Try(epr)``` will return a val if computation succeeds and a failure val if it doesn't.

### map and flatMap on a Try

+ Try can have maps and flatmaps too
+ basically it looks like: ```t.map(f) == t.flatMap(x => Try(f(x))) == t.flatMap(f andThen Try)```
+ Try is not a monad because it fails the left unit law. 
  + ```Unit(x).flatMap(f) != f(x)```
  + because ```Unit(x)``` can throw an exception.

