# Lecture 12

CS 240 - June 13, 2019

## Skip Lists

$$
\begin{align}
\text{# nodes } &= n + \frac{n}{2} + \frac{n}{4} + ...\\
&= n(1+ \frac{1}{2} + \frac{1}{4} + ...)\\
&= 2n\\
\text{height } &= \log n\\
\text{length of serach } &= \log n
\end{align}
$$

### Proof of Space Complexity

Prob(tower $j$ has height $\ge i$) = Prob($H_1 … H_i$) = $\frac{1}{2}^i$

= Prob(tower $j$ has an element at level $i$)



E(# elements at level $i$ of tower $j$)

= $1\ \times$ (Probability that tower $j$ has an element at level $i$) $+\ 0\ \times$ ($1 -$ Prob...)

= $1 \times \frac{1}{2}^i$



E(# elements in tower $j$)

 $= 1 + \frac{1}{2} + \frac{1}{4} + … + \frac{1}{2}^i + … = 2$



E(# elements in the Skiplist)

$= 2 + 2 + …. + 2 = 2n$



## Optimal Static Ordering

The ordering in decreasing probability of access minimizes its expected cost of a search.



Take the optimal ordering $L = (x_1 … x_n)$

Want: $P(x_1) > P(x_2) > … > P(x_n)$



Suppose not $P(x_i) < P(x_{i+1})$ for some $i$

Let $L' = (x_1 … x_{i+1}x … x_n)$



expected cost for $L$:

$E(L) = P(x_1) + 2P(x_2) + … + iP(x_i)+(i+1)P(x_{i+1}) + ...$

expectec cost for $L'$:

$E(L) = P(x_1) + 2P(x_2) + … + iP(x_{i+1})+(i+1)P(x_{i}) + ...$

$E(L)-E(L')=P(x_{i+1}) - P(x_i) > 0$

