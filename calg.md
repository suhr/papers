# Comma is a Product: the Algebra of Concatenative Programming

Functional programmins is universaly understood as *applicative* programming: an extension of the lambda calculus, based on application and variable binding. But this not the only way. There's an alternative approach in functional programming, named *concatenative programing*, which is based on the function composition and a set of basic combinators.

This paper introduces the algebra of two operations (composition and concatenation) as the way to define concatenative programming. The algebra provides an elegant and powerful way to combine functions without variables, which is, unlike point-free programming in applicative languages, is easy and straighforward to use.

The algebra has various useful properties that allow to introduce novel features into concatenative programming, like pattern matching without variables. This paper shows this properties without describing a way to use them.

## Introduction

Concatenative languages are often described as *stack-based* languages. A stack-based language is a sequence of *words* that operate on the *stack*. Than a word is exected, it pops some values from the stack, then does some computations with these values, and then push the resuls to the stack.

For example, in `2 3 + 5 *`, numbers `2` and `3` are pushed to the stack. Then `+` pops them from the stack and pushes `5`. Then another `5` is pushed on the stack and then `*` takes `5 5` from the stack and pushes `25`.

Additionaly, a stack-based language may also have quotations. A *quotation* is a sequence of word, enclosed in braces. This sequence is pushed to the stack as a single value, so other words can use it. For example, in `2 2 * 4 = {success} {panic} ifelse`, the word `ifelse` takes a boolean and two quotation from the stack and then executes the first quotation.

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

Traditional concatenative programming is based on only one operation on function, the (generalized) function composition. To solve the value passing problem, we introduce an additional operation — function concatenation. With this operation, function `f` can be written like this:

```
drop dup (square,square (+)),abs (-)
```

## The concatenation of functions

Before introducing the function concatenation, we will need a few definitions.

We say that a function has the *input arity* of $n$, if it *takes* $n$ values. By analogy, we say that a function has the *output arity* of $m$ if it *returns* $m$ values.

We say that a function is *simply aritied* if it has exacty one input arity and exactly one output arity.

Not every function is simply aritied. For example, the function `apply` that takes a quotation and executes it is not simply aritied, because beside a quotation it takes $n$ values to pass them to the expression inside the quotation.

Now we introduce concatenation of simply aritied functions. The concatenation of functions `f` and `g`, written as `f,g`, is a function that takes $\operatorname{Ar_{in}}(\texttt f) + \operatorname{Ar_{in}}(\texttt g)$ values, passes first $\operatorname{Ar_{in}}(\texttt f)$ values to `f` and last $\operatorname{Ar_{in}}(\texttt g)$ to `g` and returns output values of `f` followed by output values of `g`. So, if `f : a, b -> x` and `g: c,d -> y`, then `f,g : a, b, c, d -> x, y`.

To make it more clear, consider an example:

```
2 2 3 3 (*),(*) (+)
```

Here, `(*)` is a function that takes two numbers and returns their product. The concatenation `(*),(*)` is a function that takes four values and returns the product of the first two and the product of the last two. So `2 2 3 3 (*),(*)` returns `4 9` and the whole expression returns `13`.

## Categorical point of view

## Related works