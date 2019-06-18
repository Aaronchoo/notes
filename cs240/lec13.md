# Lecture 13

CS 240 - June 18, 2019



## Optimal Static Ordering

$$
C_{OPT} = \Sigma_{i=1}^n ip_i
$$

## Dynamic Ordering: MTF

For any initial ordering $I$ of $n$ keys,

let $C_{I,k} :=$ expected cost of the $k$th serach (starting from $I$ )

Then when $k \rightarrow \infty$,

$C_{I,k} \rightarrow \Sigma_{i=1}^n (1 + \Sigma_{j \ne i} \frac{pj}{pi + pj}) =: C_{MTF}$

$C_{OPT} \le C_{MTF} \le 2C_{OPT}$

## Lower bound for search

The fatest realizations of ADT Dictionary require $\Theta(\log n)$ time to search among $n$ items. Is this the best possible?

### Theorem

>In the comparison model (on the keys), $\Omega(\log n)$ comparisons are required to search a size-$n$ dictionary

### Proof

We are an algorithm for search, we count comparisons $>, <, =$.

We fix the size $n$. The possible traces of the algorithm give a decision tree (binary tree, nodes are $<$, $>$, or $=$)

There are $n+1$ leaves in the tree. ($n$ = one for each of $v_1 … v_n$, $1 = $ not found)

The height $h$ of the tree is the length of the longest path from root to a leaf. The worst case number of comparisons for this algorithm in input size $n$.

$n + 1 = \text{# leaves} \le \text{# nodes} \le 2^{h+1}-1$

so $h \ge \log(n+2)-1$

Search in an array of length 2

```pseudocode
search(A, k)
	if A[0] is k return v_0
	if A[1] is k return v_1
	return "not found"
```



## Average case of Interpolation Search

Fix $A[0], A[n-1]$

choose the other entries uniformly at random between $A[0]$ and $A[n-1]$.

Can prove with probability $\ge \frac{1}{4}$, we do a recursive call in size $\le \frac{4}{\sqrt{n}}$ 
$$
\begin{align}
	T^{avg}(n) &\le c + \frac{1}{4}T^{avg}(\sqrt{n}) + \frac{3}{4}T^{avg}(n)\\
	\frac{1}{4}^{avg}(n) &\le c + \frac{1}{4}T^{avg}(\sqrt{n})\\
	T^{avg}(n) &\le 4c + T^{avg}(\sqrt{n})\\
\end{align}\\
k = \log \log n \iff n = 2^{2^k}\\
$$

$$
\begin{align}
\sqrt{n} &= \sqrt{2^{2^k}}\\
&= 2^{\frac{2^k}{2}}\\
&= 2^{2^{k-1}}
\end{align}
$$

$$
\begin{align}
T^{avg}(n) &\le T^{avg}(2^{2^k})\\
&\le 4c + T(2^{2^{k-1}})\\
&\le 4c + 4c + T(2^{2^{k-2}})\\
&\le ...\\
&\le \Sigma_1^k 4c + T(2)\\
&\le 4ck + c'\\
&\in \Theta(k)\\
&\in \Theta(\log \log k)
\end{align}
$$

