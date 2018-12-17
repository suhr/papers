Title: Operational Semantics of the Concatenative Algebra

Application is an operation on a function and a value. Contrary to that, both composition and concatenation are operations on functions.

But computation is the process of transforming values. There're two ways to solve this problem:

1. Extend composition and concatenation for values
2. Separate values from functions, so expressions consist only of functions

We will use the second approach. So a program transforms a tuple of values (traditionally called as *stack*) into another tuple. It can be written as

$$P(S_1) = S_2$$

Stack values are written using angular parenthesis, like so: $⟨1, 2⟩$. A concatenation of stacks is written as $S_1 ∪ S_2$, for example, $⟨1, 2⟩ ∪ ⟨3, 2⟩ = ⟨1, 2, 3, 2⟩$.

An computation state is a pair of the stack and the program, which we write as $S\ P$.

$$S_1\ P ⟶ S_2$$


## The semantics of the CA

Let's define the reduction semantics of the concatenative algebra.

Most functions are defined outside of the concatenative algebra. An example of such function is the addition: $add(⟨a, b⟩) = ⟨a + b⟩$.

The reduction rule for these functions looks like this:

$$S\ P\leadsto P(S) \tag{Ext}$$

But primitive rewiring functions are a part of the concatenative algebra:

$$
\begin{align}
⟨a⟩\ \mathtt{id} &\leadsto ⟨a⟩ \\
⟨a⟩\ \mathtt{dup} &\leadsto ⟨a,a⟩ \\
⟨a⟩\ \mathtt{drop} &\leadsto ⟨⟩ \\
⟨a,b⟩\ \mathtt{swap} &\leadsto ⟨b,a⟩
\end{align}
\tag{Prim}
$$

Now the rule for composition:

$$S\ (P∘Q) \leadsto (S\ P)\ Q \tag{Comp}$$

This rule means that in order to evaluare $S\ (P∘Q)$ you have to evaluate $S\ P$ first.

Concatenation:

$$
\begin{align}
\begin{split}
S\ (P\mathbin{,}Q) &\leadsto (S_1\ P) ∪ (S_2\ Q) \\
\quad
\end{split}
\qquad
\begin{split}
S_1, S_2 &= \mathop{\mathrm{split}}(n, S) \\
n &= \mathop{\mathrm{Ar}}_\nolimits\mathrm{in}(P)
\end{split}
\end{align}
\tag{Conc}
$$

In order to evaluate $S\ (P\mathbin{,}Q)$, you split the stack in two parts: $S_1$ and $S_2$. The $S_1$ contains first $\mathop{\mathrm{Ar}}_\nolimits\mathrm{in}(P)$ elements of $S$ and $S_2$ contains reminding elements of $S$. Then you evaluate $S_1\ P$ and $S_2\ Q$ and concatenate the results.

Note that this rule implies a prior knowledge of input arity of $P$. The most practical way to obtain this value is the type checking. But there's also an alternative way, where the stack is splitted nondeterministically and then all stuck states are cut.

Higher-order programming:

$$
\begin{align}
⟨⟩\ \mathtt{\{P\}} &\leadsto ⟨P⟩ \\
S ∪ ⟨P⟩\ \mathtt{apply} &\leadsto S\ P
\end{align}
\tag{Higher}
$$

Metaprogramming:

$$
\begin{align}
⟨a⟩\ \mathtt{unit} &\leadsto ⟨\mathop{\mathrm{unit}}(a)⟩ \\
⟨P,Q⟩\ \mathtt{comp} &\leadsto ⟨P ∘ Q⟩
\end{align}
\tag{Meta}
$$

## Extension: lambda expressions

$$S ∪ ⟨a⟩\ (\mathtt{λ v. P}) \leadsto S\ P[v := \mathop{\mathrm{unit}}(a)] \tag{Lam}$$
