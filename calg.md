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
%define the function recursively:
/fib { dup
       3 lt
         { pop 1 }
         { dup 1 sub fib exch 2 sub fib add }
       ifelse
    } def
```

Stack-based languages were believed to be imperative. But in [Joy](http://www.kevinalbrecht.com/code/joy-mirror/joy.html), Manfred von Thun [showed](http://www.kevinalbrecht.com/code/joy-mirror/j02maf.html) that stack-based programming can be described in a pure functional way, as composition of functions that pass the stack to eash other. He also introduced the term *concatenative* for languages that are based on function composition.

## The value passing problem

## Categorical point of view