# Lecture 7

CS 240 - May 28, 2019

## QuickSelect

after partition

`A = [...<=v,v,...>=v]`

`v` is at position `i`

if `k < i` then look in `A[0...i-1]`, index `k`

if `k > i` then look in `a[i+1...n-1]`, index `k-i-1`

### Worst Case Analysis

Recursive call would always have size n - 1

`A = [0, ..., n-1]`

`k = 0` 

after partition `A = [0,...,n-1]`, `i = n-1`

call `QuickSelect(A[0, ... , n-2], 0)`

Recurrence given by
$$
T(n) =
\begin{cases}
T(n-1)+cn,&n\ge2\\
c, &n=1
\end{cases}
$$
Solution: $T(n) = cn + c(n-1) + c(n-2) + … + c \cdot 2 + c \in \Theta(n^2)$

### Best Case Analysis

First chosen pivot could be the kth element

No recursive calls: total cost is $\Theta(n)$

### Average Case Analysis

Take $n = 3, k \in \{0,1,2\}$

on average,
$$
\begin{align}
T(3, k) = \frac{1}{6}(T([0,1,2],k) + T([1,0,2],k)) + T([0,2,1],k)) + T([2,0,1],k)) + T([1,2,0],k))+ T([2,1,0],k))
\end{align}
$$
__Goal__: $T(n,k) \le 4cm$, where $c$ is the constant of partition

#### Average Runtime

$$
\begin{align}
T(n,k)&=\frac{1}{n}(cn) + \sum_{i=0}^{k-1}\frac{1}{n}(cn + T(n-i-1,k-i-1)) + \sum_{i=k+1}^{n-1}T(i,k)
\end{align}
$$



#### Proof

- For $i = 0, …, n-1$, a fraction of $\frac{1}{n}$ of all permutations has pivot index $i$
- The average run time for these permutation is

$$
\begin{cases}
cn+T(n-i-1,k-i-1) &\text{ if }i < k\\
cn &\text{ if }i = k\\
cn+T(i,k) &\text{ if }k < i
\end{cases}
$$

#### Theorem

$T(n,k)\le 4cn$

#### Proof

By induction on n

**Base Case (n = 1)**

$T(1,k)=c \le 4cn$

**Induction Step**

Assume true for indices $i, …, n-1$, prove it for $n$
$$
\begin{align}
T(n,k)&=cn + \frac{1}{n}(\sum_{i=0}^{k-1} T(n-i-1,k-i-1) + \sum_{i=k+1}^{n-1}T(i,k))\\
&\le cn+\frac{1}{n}(\sum_{i=0}^{k-1} 4c(n-i-1) + \sum_{i=k+1}^{n-1}4ci)\\
&\le cn+\frac{4c}{n}(\sum_{i=0}^{k-1} (n-i-1) + \sum_{i=k+1}^{n-1}i)\\
&\le cn+\frac{4c}{n}(\frac{4}{3}n^2)\\
&\le cn+3cn\\
&= 4cn
\end{align}
$$
