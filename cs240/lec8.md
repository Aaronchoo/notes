# Lecture 8

CS 240 - May 30, 2019

## Randomized QuickSelect:

### First Idea - Shuffle

Randomly permute the input first using shuffle:

```pseudocode
shuffle(A):
	for i <- 0 to n - 2 do:
		swap(A[i],A[i + random(n - i)])
```

Expected cost becomes the same as average cost: $O(n)$

### Example

`A = [1,2,3]`

`n = 3`

When $i = 0$
$$
\begin{cases}
\texttt{A=[1,2,3]} &random = 0
\begin{cases}
\texttt{A=[1,2,3]}\\
\texttt{A=[1,3,2]}\\
\end{cases}\\
\texttt{A=[2,1,3]} &random = 1
\begin{cases}
\texttt{A=[2,1,3]}\\
\texttt{A=[2,3,1]}\\
\end{cases}\\
\texttt{A=[3,2,1]} &random = 2
\begin{cases}
\texttt{A=[3,2,1]}\\
\texttt{A=[3,1,2]}\\
\end{cases}\\
\end{cases}
$$

$$
\begin{align}
T^{\text{exp}}([1,2,3], k) &= \frac{1}{6}(T([1,2,3],k) + ... +T([3,1,2],k)) + \text{cost of shuffle}
\end{align}
$$

$\frac{1}{6}(T([1,2,3],k) + ... +T([3,1,2],k)) = T(n,k) \in \Theta(n)$

$\text{cost of shuffle} \in \Theta(n)$

### Second Idea - Random Pivot

Randomly choose the pivot

```pseudocode
choose-pivot2(A):
	return random(n)

quick-select2(A,k):
	p <- choose-pivot2(A)
```



With probability $\frac{1}{n}$ the random pivot has index $i$, so the analysis is just like that for the average case. The expected running time is again $\Theta(n)$

This is generally the fastest quick-select implementation

There exists a variation that has worst-case running time $\Theta(n)$, but it uses double recursion and is slower in practise.

## QuickSort

Hoare developed partition and quick-select in 1960.

```pseudocode
quick-sort1(A):
	if n <= 1 then return
	p <- choose-pivot2(A)
	i <- partition(A,p)
	quick-sort1(A[0...i-1])
	quick-sort1(A[i+1...n-1])
```

### QuickSort Analysis

Defin $T(n)$ to be the run time for `quick-sort1` in a size-n array

- $T(n)$ depends again on pivot index $i$
- if we know $i$: $T(n) = \Theta(n) + T(i) + T(n-i-1)$

#### Worst Case

$i = 0$ or $n-1$ always. then as for quick-select
$$
T(n)=
\begin{cases}
T(n-1)+cn &n\ge2\\
c &n=1
\end{cases}
$$
for some constant $c > 0$. This resolves to $\Theta(n^2)$

#### Best Case

$i = \lfloor\frac{n}{2}\rfloor$ or $\lceil\frac{n}{2}\rceil$ always, then
$$
T(n)=
\begin{cases}
T(\lfloor\frac{n}{2}\rfloor)+T(\lceil\frac{n}{2}\rceil)+cn &n\ge2\\
c &n=1
\end{cases}
$$
Similar to merge-sort. This resolves to $\Theta(n\log n)$

#### Average Case

$$
\begin{align}
T(n) &= \frac{1}{n!}\sum_{\text{I permutation of \{0...n-1\}}}T(I)\\
&= \frac{1}{n!}\sum_{i=0}^{n-1}\sum_{\text{I: size(I)=n}\\\text{I has pivot i}} T(I)\\
&\le \frac{1}{n!}\sum_{i=0}^{n-1}(n-1)!(c\cdot n+T(i) +T(n-i-1))\\
&= c \cdot n + \frac{1}{n}\sum_{i=0}^{n-1}(T(i) + T(n - i -1))
\end{align}
$$



**Theorem**: $T(n) \in \Theta(n\log n)$

**Proof**: Can prove that $T(n) \le 2cn\log(n)$ by induction on $n$

### Improvement Ideas for QuickSort

- We can randomize by using choose-pivot2, giving $\Theta(n\log n)$ expected time for quick-sort2
- The auxiliary space is $\Omega(\text{recursion depth})$
  - this is $\Theta(n)$ in the worst-case
  - it can be reduced to $\Theta(\log n)$ worst case by recursving in smaller sub array first and replacing the other recursion by a while loop
- One should stop recursing when $n\le10$. One run of InsertionSort at the end the nsorts everything in $O(n)$ time since all items are within 10 units of their required position.

## Decision Trees

comparison-based algorithms can be expressed as a decision tree.

- each leaf is labelled by a permutation

- the depth of this leaf is the number of comparisons that the algorithm does for that input

- the height $h$ of the tree is the worst case number of comparisons

  $\text{# leaves } = n! \le \text{# nodes} \le 2^{h+1}-1 \le 2^{h+1}$

  $\implies \log(n!) \le h + 1$

  so $\log(n!) \in \Theta(n\log n)$ so $h \in \Omega(n \log n)$

