# Lecture 10

CS 240 - June 6, 2019

## Proof of height of a heap is $O(\log n)$

$$
2^h \le \text{ # nodes in a heap of height h } \le 2^{h+1}-1\\
\begin{align}
&\implies 2^h \le n\\
&\implies h \in O(\log n)
\end{align}
$$

## Proof of height of an AVL with n nodes is $O(\log n)$

1. Define $N_h$ the minimum number of nodes in an AVL of height $h$

2. Find a lower bound on $N_h \ge (\sqrt{2})^h$
3. Take a log.

$(\sqrt{2})^h \le N_h \le n$

$h(\log \sqrt{2}) \le \log n$

$h \le 2\log n$
$$
\begin{align}
N_h &= 1 + N_{h-1} + N_{h-2}\\
&\ge 1 + 2N_{h-2}\\
&\ge 1 + 2(1+2N_{h-4})\\
&= 3 + 4N_{h-4}\\
&\ge 3+4(1+2N_{h-6})\\
&=7+8N_{h-8}\\
&\ge 15+16N_{h-8}\\
&=(2^4-1)+2^4N_{h-24}\\

N_h &= (2^{\frac{h}{2}}-1)+2^{\frac{h}{2}}\\

N_0 &\ge 2^{\frac{h}{2}}\\
&= \sqrt{2}^h
\end{align}
$$


