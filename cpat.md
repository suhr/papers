Title: Pattern Matching in Concatenative Languages

In [Comma is a Product: the Algebra of Concatenative Programming](./calg.html) we described the algebra of concatenative programming. In this paper, we introduce a way to define the concatenative pattern matching without using variables, by investigating the cancellation properties of the algebra.

## Introduction

An algebraic data type is a data type defined by a list of *data constructors*. For example:

```
data Stack[T] where
    Nil : -> Stack[T]
    Cons : Stack[T], T -> Stack[T]
```

Any value of the ADT is can be constructed by using data constructors and primitive values. Like so:

```
Cons[Cons[Cons[Nil, 1], 2], 3]
```

Pattern matching is a way to deconstruct an ADT value by matching it against a *pattern*:

```
case stack:
    Cons[$xs, $x] ->
        ...
    Nil ->
        ...
```

Here, the value of `stack` is matched against two patterns: `Cons[$xs, $x]` and `Nil`. In the `Cons[$xs, $x]` branch, the corresponding values are binded to variables `$xs` and `$x`. For example, if `stack` is `Cons[Cons[Cons[Nil, 1], 2], 3]`, then `$xs` is `Cons[Cons[Nil, 1], 2]` and `$x` is `3`.

In functional languages, algebraic data types and pattern matching rely on application and variable binding. Our approach relies on the cancellation properties of the concatenative algebra.

## Pattern matching as cancellation

Expression like `(((Nil,1 Cons),2 Cons),3 Cons)` are built using only data constructors and values. Let's call such example as simple constructors.

**Proposition:** every simple constructor can be rewritten as `vs cs`, where `vs` is a concatenation of values and `cs` is a function built from data constructors and `id`.

**Proof:** The value elimination theorem and the substructural properties of the concatenative algebra.

For example,`(((Nil,1 Cons),2 Cons),3 Cons)` can be written as `1,2,3 (((Nil,id Cons),id Cons),id Cons)`. 

In an algebra with operation \\(\*\\), the element $c$ is *right-cancellative* if for any $a$ and $b$ the following is true:

$$a * c = b * c \quad ⇒ \quad a = c$$

In other words, $c$ is cancellative if it can be undone in an equation. An algebra is right-cancellative if all elements in the algebra are right-cancellative.

We will use a different, more restricted definition. We say that $c$ is right-cancellative if there exists such function $\tilde\, c$ so the following is true for any $a$:

$$\tilde\, c (a * c) = a$$

Note that $\tilde\, c$ is only valid for values obtained by applying $(\cdot * c)$.

**Proposition:** Let $C$ be a concatenative algebra of values, data constructors and `id`. Then $C$ is right-cancellative over the composition.

**Proof:**

1. The cancellation property of data constructors and `id` is trivial
2. The cancellation of `a,b` is `~a,~b`, where `~a` and `~b` are cancellations of `a` and `b`
3. The cancellation of `a b` is `~b ~a`

For example, the cancellation of `(id,id Cons),3 Cons` is `~Cons (~Cons id,id),drop`.

This is the essence of concatenative pattern matching.

## Compiling to conditionals

## Extensions

## Related works
