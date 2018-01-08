Title: Pattern Matching in Concatenative Languages

In [Comma is a Product: the Algebra of Concatenative Programming](./calg.html) we described the algebra of concatenative programming. In this paper, we introduce a way to define the concatenative pattern matching without using variables, by investigating the cancellation properties of the algebra.

## Introduction

Pattern matching is a way to deconstruct data by matching it with a pattern. It is the standard way to work with the algebraic data types. [TODO: write something]

Algebraic data type is a data type defined by a list of primitive *data constructors*. For example:

```
data Stack[T] where
    Nil : -> Stack[T]
    Cons : Stack[T], T -> Stack[T]
```

Pattern matching:

```
case stack:
    Cons[xs, x] ->
        ...
    Nil ->
        ...
```

## Pattern matching as cancellation

**Proposition:** every constructable value can be constructed in the concatenative algebra using only data constructors and the identity function (`id`).

This is the direct consequence of the value elimination theorem.


TODO: write moar

**Proposition:** the concatenative algebra is right-cancellative on the set of constructing functions.

## Compiling to a decision tree

## Related works
