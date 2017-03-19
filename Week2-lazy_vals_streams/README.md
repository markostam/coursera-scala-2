# Lazy Values and Streams

## Streams

+ Like lists, tail evaluated only on demmand.
+ Allow you to write elegant/efficient solutions to search problems.
+ Performance problem ```((1000 to 10000) filter isPrims)(1)```
  + is pretty bad for memory/performance; it constructs all prime numbers between 1k and 10k in a list, but only looks at the first two elements of that list.
  + however, we don't know when the first two primes will occur, so we search all the way up to 10k to make sure we get two primes. i.e. reducing upper bound would speed things up but risks missing the second prime.
+ We can make the code efficient with this *one simple trick!*
  + use a stream. like a list, tail evaluated only on demmand.
+ ***Streams are kind of like generators in Python. Evaluated only on demmand.***

### Defining streams

1) ```val xs = Stream.cons(1, Stream.cons(2, Stream.empty))```
  + Stream.cons(x) === List :: x 
2) ```Stream(1,2,3)```
3) ```(1 to 100).toStream```

+ ```x #:: xs == Stream.cons(x, xs)```
