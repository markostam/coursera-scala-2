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
