# Lecture 14

CS 240 - June 20, 2019

## Interpolation Search

- Similar to binary search, but compare at interpolated index
- Need a few extra tests to avoid crash due to $A[l] = A[r]$

## Tries

> **Trie** (aka **radix tree**): A dictionary for bitstrings
>
> - Comes from retrieval, but pronounced "try"
> - A tree bbased on bitwise comparisons: Edge labelled with corresponding bit
> - Similar to radix sort: use individual bits, not the whole key

### Assumption

Dictionary is prefix-free: no string is a prefix of another

- Assumption satisfied if all strings have the same length
- Assumption satisfied if all strings end with 'end-if-word' character $.



## Compressed Tries

If $T$ is a tree where every internal node have at least 2 children

let $in(T) = $ number of internal nodes of $T$

let $l(T) = $ number of leaves of $T-

then $in(T) \le l(T) - 1$ 



### Proof by induction o nthe number $k$ of nodes in $T$.

#### Base Case

**$k = 1$**

$T = 0, l(0) = 1, in(0) = 0$

$0 \le 1 - 1$



#### Induction Step

Suppose that for trees with $1, …, k$ nodes. Take $T$ with k nodes

$m =$ number of children of the root

$T_1 … T_m = $ subtress at the root

$m \ge 2$ (assumption on $T$)

(note: all $T_i$'s satisfy the induction assumption')



Internal nodes in $T$ are either the root or internal nodes in the $T_i$;s.

so $in(T) = 1 + in(T_1) + … + in(T_m)$



Leaves in $T$ are the leaves of the $T_i$'s 

so $l(T) = l(T_1) + … + l(T_m)$


$$
\begin{align}
in(T) &= 1 + in(T_1) + ... + in(T_m)\\
&\le 1 + (l(T_1)-1) + ... + (l(t_m)-1)\\
&\le l(T_1) + ... + l(T_m) - m + 1\\
&\le l(T) - 1
\end{align}
$$


