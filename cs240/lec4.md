# Lecture 4

CS 240 - May 16, 2019

---

## Merge Sort

### Merge Example

n = 5

l = 0

r = 4

$m =\frac{l + r}{2} = 2$

First set `S` to `A`

`S = [2, 4, 7, 5, 6]`

$i_L = 0, i_R = m + 1 = 3$

After `k = 0`, `A = [2]`, $i_L = 1, i_R = 3$

After `k = 1`, `A = [2, 4]`, $i_L = 2, i_R = 3$

After `k = 2`, `A = [2, 4, 5]`, $i_L = 2, i_R = 4$

After `k = 3`, `A = [2, 4, 5, 6]`, $i_L = 2, i_R = 5$

Since $i_R = 5$ is out of bounds, copy the left part into the array

`A = [2, 4, 5, 6, 7]`

### Analysis of Merge Sort

$T(n) = 2T(\frac{n}{2}) + cn, n > 1$

$T(1) = c$
$$
\begin{align}
n &= 2^k\\
&=T(2^k)= 2T(2^{k-1}) + c2^k\\
&= 2(2T(2^{k-2})+ c2^{k-1})+c2^k\\
&=2^2T(2^{k-2})+2c2^k\\
&=2^2(2T(2^{k-3})+ c2^{k-2})+2k2^k\\
&=2^3T(2^{k-3})+3c2^k\\
&=2^4T(2^{k-4})+4c2^k\\
&=...\\
&=2^kT(2^{k-k})+kc2^k\\
&=w^kT(1)+kc2^k\\
&=c2^k(k+1)\\
\end{align}
$$

$$
\begin{align}
T(2^k) &= c2^k(k+1)\\
T(n) &= cn(log(n)+1)
\end{align}
$$



