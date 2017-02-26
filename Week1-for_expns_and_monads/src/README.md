# For-expressions and Monads

## Recap Fucntions and Pattern Matching

### Functions are objects

+ In scala, every type is the type of some class or trait. Function type is no exception
+ ```JBinding => String ```
is shorthand for
+ ```scala.Function1[JBinding, String]```
+ where ```scala.Function1``` is a train and ```JBinding, String``` are its type arguments

### Partial Functions

+ you can change a pattern match functino to a ```PartialFunction``` which automatically gives your function an ```isDefinedAt``` method.

## Recap: Collections

### Translation of For

1. ```for(x <- e1) yield e2``` translates to ```e1.map(x => e2)```
2. ``` for (x <- e1 if f; s) yield e2 ``` translates to ```for (x <- e1.withFilter(x => f); s) yield e2```
  + ```withFilter``` is like ```filter``` but does not produce an intermediate list, but instead filters the following ```map``` or ```flatmap``` application.
3. ```for(x <- e1; y <- e2; s) yield e3``` tranlsates to ```e1.flatMat(x => for (y <- e2; s) yield e3)```
  + and then the translation continues with the new expression
  
### For expressions and pattern matching

+ the left-hand side of a generator can also be a pattern
+ you pull a value from the ```x <- e1``` statement above and assign a pattern to it, this basically becomes a filter or withFilter 

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
