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


