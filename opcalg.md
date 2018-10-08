Title: Operational Semantics of the Concatenative Algebra

$$S\ P\leadsto P(S) \tag{Ext}$$

$$
\begin{align}
⟨a⟩\ \mathtt{id} &\leadsto ⟨a⟩ \\
⟨a⟩\ \mathtt{dup} &\leadsto ⟨a,a⟩ \\
⟨a⟩\ \mathtt{drop} &\leadsto ⟨⟩ \\
⟨a,b⟩\ \mathtt{swap} &\leadsto ⟨b,a⟩
\end{align}
\tag{Prim}
$$

$$S\ (P∘Q) \leadsto (S\ P)\ Q \tag{Comp}$$

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

$$
\begin{align}
⟨a⟩\ \mathtt{unit} &\leadsto ⟨\mathop{\mathrm{unit}}(a)⟩ \\
⟨P,Q⟩\ \mathtt{comp} &\leadsto ⟨P ∘ Q⟩
\end{align}
\tag{Meta}
$$

$$
\begin{align}
⟨⟩\ \mathtt{\{P\}} &\leadsto ⟨P⟩ \\
S ∪ ⟨P⟩\ \mathtt{apply} &\leadsto S\ P
\end{align}
\tag{Higher}
$$

$$S ∪ ⟨a⟩\ (\mathtt{λ v. P}) \leadsto S\ P[v := \mathop{\mathrm{unit}}(a)] \tag{Lam}$$

