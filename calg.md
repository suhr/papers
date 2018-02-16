Title: Comma is a Product: the Algebra of Concatenative Programming

Functional programmins is universaly understood as *applicative* programming: an extension of the lambda calculus, based on application and variable binding. But this not the only way. There's an alternative approach in functional programming, named *concatenative programing*, which is based on the function composition and a set of basic combinators.

This paper introduces the algebra of two operations (composition and concatenation) as the way to define concatenative programming. The algebra provides an elegant and powerful way to combine functions without variables, which is, unlike point-free programming in applicative languages, is easy and straighforward to use.

The algebra has various useful properties that allow to introduce novel features into concatenative programming, like pattern matching without variables. This paper shows this properties without describing ways to use them.

## Introduction

Concatenative languages are often described as *stack-based* languages. A stack-based language is a sequence of *words* that operate on the *stack*. When a word is exected, it pops some values from the stack, then does some computations with these values, and then push the resuls to the stack.

For example, in `2 3 + 5 *`, numbers `2` and `3` are pushed to the stack. Then `+` pops them from the stack and pushes `5`. Then another `5` is pushed on the stack and then `*` takes `5 5` from the stack and pushes `25`.

Additionaly, a stack-based language may also have quotations. A *quotation* is a sequence of words, enclosed in braces. The quotation is pushed to the stack as a single value, so other words can use it. For example, in `2 2 * 4 = {success} {panic} ifelse`, the word `ifelse` takes a boolean and two quotations from the stack and then executes the first quotation.

PostScript is one of the most-known stack-based languages. This is an example of PostScript code:

```
/fib { dup
       3 lt
         { pop 1 }
         { dup 1 sub fib exch 2 sub fib add }
       ifelse
    } def
```

Stack-based languages were believed to be imperative. But in [Joy](http://www.kevinalbrecht.com/code/joy-mirror/joy.html), Manfred von Thun [showed](http://www.kevinalbrecht.com/code/joy-mirror/j02maf.html) that stack-based programming can be described in a pure functional way, as composition of functions that pass the stack to eash other. He also introduced the term *concatenative* for languages that are based on function composition.

By using the stack, concatenative language provide a way to write elegant code without variables. But that leads us to a problem...

## The value passing problem

Variables are a very powerful way to pass values from one function or another. That's why you can find them in every popular programming languages. Concatenative languages prefer to avoid variables (some languages don't even have then) and rely on the stack as the way to pass values. The problem is the stack is a rather opaque way to pass values.

Consider a simple example. Let you have a function

$$f(x, y, z) = x^2 + y^2 - |y|$$

In a language with variables, such function is trivial to implement. Now this is how it looks in a concatenative language without using combinators (functions that take a quotation):

```
drop dup dup × swap abs rot3 dup × swap − +
```

As you can see, this involves quite a stack juggling together with special ad-hoc words like `rot3`. Using combinators make the code more clear:

```
drop [square] [abs] bi − [square] dip +
```

Unfortunately, it requires ad-hoc combinators like `bi`. Beside that, it is still hard to read. It is also hard to analyze. In fact, if the set of combinators is turing complete, the analysis is undecideable.

Traditionaly, concatenative programming is based on only one operation on function, the (generalized) function composition. To solve the value passing problem, we introduce an additional operation — function concatenation. With this operation, function `f` can be written like this:

```
drop dup (square,square (+)),abs (-)
```

## The concatenation of functions

Before introducing the function concatenation, we will need a few definitions.

We say that a function has the *input arity* of $n$, if it *takes* $n$ values. By analogy, we say that a function has the *output arity* of $m$ if it *returns* $m$ values.

A *fed function* is a functon of input arity 0.

We say that a function is *simply aritied* if it has exacty one input arity and exactly one output arity.

Not every function is simply aritied. For example, the function `apply` that takes a quotation and executes it is not simply aritied, because beside a quotation it takes $n$ values to pass them to the expression inside the quotation.

Now we introduce concatenation of simply aritied functions. The concatenation of functions `f` and `g`, written as `f,g`, is a function that takes $\operatorname{Ar_{in}}(\texttt f) + \operatorname{Ar_{in}}(\texttt g)$ values, passes first $\operatorname{Ar_{in}}(\texttt f)$ values to `f` and last $\operatorname{Ar_{in}}(\texttt g)$ to `g` and returns output values of `f` followed by output values of `g`. So, if `f : a, b -> x` and `g: c,d -> y`, then `f,g : a, b, c, d -> x, y`.

To make it more clear, consider an example:

```
2 2 3 3 (*),(*) (+)
```

Here, `(*)` is a function that takes two numbers and returns their product. The concatenation `(*),(*)` is a function that takes four values and returns the product of the first two and the product of the last two. So `2 2 3 3 (*),(*)` returns `4 9` and the whole expression returns `13`.

Note that the concatenation has a higher priority than the composition, so `a b,c` is `a (b,c)`, not `(a b),c`. Just like the composition, the concatenation forms a monoid over pure functions, so you can write `a,b,c` without using parenthesises.

The concatenation provides an alternative interpretation of concatenative programming, without using an explicit stack.

Let you have a function `f` that takes $n$ values and returns $m$ values. Let `id` be the identity function of arity of 1. For any natural $k$, there's an equivalent function `id,id,...,id,f`, that takes $k + n$ values and returns $k + m$ values.

Let `g` be a function of input arity $p$ and output arity $q$. We say, that `f g` is a *proper composition* of functions `f` and `g`, if $\operatorname{Ar_{out}}(\texttt f) = \operatorname{Ar_{in}}(\texttt g)$. In the general case, we say that `f g` is a *generalized composition*.

We say, that an expression is in the *canonical concatenative form* (CCF), if every composition in that expression is a proper composition.

For example, let you have an expression `1 2 sqrt (+) 2 (/)`. The canonical concatenative form of this expression will be `1,2 id,sqrt (+) id,2 (/)`.

Every expression can be recursively transformed into CCF. That means, that the generalized composition is merely a *semantic sugar*, achieved by implicitly concatenating identity functions. Neither concatenation nor proper composition rely on the concept of the stack. Thus, concatenative programming doesn't require it at all, it's just a convenient way to think about the data flow.

The concatenation also allows to introduce an infix notation as a syntactic sugar. Let `f` and `g` are functions, and `op` is an operator. Then `f op g` desugars to `f,g op`.

This sugar is very convinient. For example, it allows to rewrite
`drop dup (square,square (+)),abs (-)`

as

`drop dup square + square - abs`.

## The variable elimination theorem

There should be a good reason to introduce a new primitive operation on functions. In the case of the concatenation, the following theorem provides a good motivation for this.

Suppose, you have a concatenative expression with variables, like `1 / (3 * x^2)`. The expression is a fed function, it doesn't take any values. In concatenative programming, you may want to eliminate varibles in the expression, so it would take values instead of using variables. In out case, `1 / (3 * id^2)` is a point-free version of the expression.

It's finds out, there's a way to eliminate variables in any expression without quotations.

**Proposition 1:** Let `expr` be a fed concatenative expression without quotations, and let `t1`...`tN` are variables used in that expression. Then there exists an expression `pf` without variables, such as `t1,...,tN pf` returns the same values as `expr`.

To prove the proposition 1, we need to proof a small lemma first. A *rewiring function* is a function which output consists of a subset of the function's input values. Values can be droped, duplicated or reordered. For example, `a b c d -> a c b c`  is a rewiring function that drops `d` and duplicates `c`.

Let we have some primitive rewiring functions: `dup` is `a -> a a`, `swap` is `a b -> b a`, `drop` is `a -> `, `id` is `a -> a`. Then the following is true.

**Proposition 1.1 (the rewiring lemma):** Any rewiring function can be constructed using only composition, concatenation and primitive rewiring functions.

⏵To construct the rewiring function, we need to construct the function that drops and duplicates values and the function that reorders values. The composition of these function is the required rewiring function.

The first function is `f1,f2,...,fN` where `fn` is `drop` or `dup`, `dup2`, ..., `dupK` — a function that takes a value and return the value $k$ times. Such function can be constructed this way: `dup id,dup id,id,dup ... id,...,id,dup`.

The second function is a permutation. It can be constructed as a composition of pairwise permutations `id,...,id,swap,id,...,id`.⏴

For example,  `a b c d -> a c b c` is `id,id,dup,drop id,swap,id`. The first concatenation is `a b c d -> a b c c`, and the second is `a b c d -> a c b d`. 

Now we can construct the point-free expression recursively. To do so, we mark some function input values with variable names. Then:

- `tN` becomes `id` with a marked input
- Concatenation works as usually, but preserves the input marks

Composition is a little bit more tricky. Let we have `f g`:

1. Because `g` has additional marked inputs, the output arity of `f` doesn't match the input arity of `g`. We fix this by concatenating corresponding marked `id`s to `f`.
2. Then we construct `f w g`, where `w` is a rewiring function that connects marked `id`s to corresponding marked inputs of `g` and connects unmarked outputs of `f` to corresponding unmarked inputs of `g`.

The resulting point-free expression can be rewired to to get the required `pf` function.

## Substructural properties

## Categorical point of view

## Related works
