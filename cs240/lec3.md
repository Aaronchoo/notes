# Lecture 3

CS 240 - May 14, 2019

---

## Techniques for Order Notation

### L'hopital's Rule

if $lim_{\infty} f(n) = lim_{\infty} g(n) = \infty$

### Example 3

$f(n) = log(n)=\frac{ln(n)}{ln(2)}$

$g(n) = n$

$f'(n) = \frac{1}{ln(2)}$

$g'(n) = 1$

so
$$
lim_{\infty} \frac{f'(n)}{g'(n)} = 0 => \text{l'hopital}
$$

$$
lim_{\infty} \frac{f(n)}{g(n)} = 0 => \text{Limit Test}
$$

$$
f(n) \in o(g(n))
$$

### Example

$f(n) = log(n)$

$g(n) = n^a, a > 0$
$$
\frac{f'(n)}{g'(n)} = \frac{1}{ln(2)}\frac{1}{n}\frac{1}{a}\frac{1}{n^{a-1}} = \frac{1}{ln(2)}\frac{1}{a}\frac{1}{n^a}
$$
As Before
$$
lim_{\infty} \frac{f'(n)}{g'(n)} = 0 => \text{l'hopital}
$$

$$
lim_{\infty} \frac{f(n)}{g(n)} = 0 => \text{Limit Test}
$$

$$
f(n) \in o(g(n))
$$

### Example

$f(n) = log(n)^c$

$g(n) = n^d$
$$
\frac{f(n)}{g(n)} = \frac{log(n)^c}{n^d} = \frac{log(n)^c}{(n^{\frac{d}{c}})^c} = (\frac{log(n)}{n^{\frac{d}{c}}})^c
$$
Taking $a = \frac{d}{c}$, we saw that $lim_{\infty} \frac{log(n)}{n^{\frac{d}{c}}} = 0$

so $\lim_{\infty} (\frac{log(n)}{n^{\frac{d}{c}}})^c = 0$

so $f(n) \in o(g(n))$

## Common Growth Rates

- $\Theta(1)$ constant complexity
- $\Theta(log n)$ logarithmic complexity
- $\Theta(n)$ linear complexity
- $\Theta(nlogn)$ linearithmic complexity
- $\Theta(nlog^kn)$, for some constant k (quasi-linear)
- $\Theta(n^2)$ quadratic complexity
- $\Theta(n^3)$ cubic complexity
- $\Theta(2^n)$ exponential complexity

### How Growth Rates Affect Running Time

#### Example

If $T(n) = clog(n)$,
$$
\begin{align}
T(2n) &= clog(2n)\\
&= c(log2+logn)\\
&= c + clog(n)\\
&= c + clog(n)\\
&= c + T(n)
\end{align}
$$

#### Example

If $T(n) = c2^n$,
$$
\begin{align}
T(2n) &= c2^{2n}\\
&= c(2^n)^2\\
&= c(\frac{T(n)}{c})^2\\
&= \frac{T(n)^2}{c}
\end{align}
$$

$$
\begin{align}
T(n+1) &= c2^{n + 1}\\
&= 2c2^n\\
&= 2T(n)
\end{align}
$$

## Techniques for Algorithm Analysis

Goal: Use asymptotic notation to simplify run-time analysis

Running time of an algorithm depends on the input size n

### Example

```pseudocode
Test1(n)
	sum <- 0
	for i <- 1 to n do
		for j <- i to n do
			sum <- sum + (i - j)^2
	return sum
```

Let $T_1(n)$ be the runtime of $Test1(n)$

Then $T_1(n) \in \Theta(S_1(n))$ where $S_1(n)$ is the number of time we enter step 4​
$$
S_1(n) = \sum_{i=1}^{n}\sum_{j = i}^{n}1\\
\sum_{j = i}^{n} 1 = n - i + 1\\
\begin{align}
\text{so}\ S_1{n} &= \sum_{i =1}^{n} (n - i + 1)\\
&= \sum_{i = 1}^{n}n - \sum_{i = 1}^{n} i + \sum_{i = 1}^{n} 1\\
&= n^2 - \frac{n(n+1)}{2} + n\\
&= \frac{1}{2}n^2 + \frac{1}{2}n \in \Theta(n^2)
\end{align}
$$
so $T_1(n) \in \Theta(n^2)$

### Two general strategies

1. Use $\Theta$-bounds throughout the analysis and obtain a $\Theta$-bound for the complexity of the algorithm
2. Prove a $O$-bound and a matching $\Omega$-bound separately. Use upper bounds (for $O$-bounds) and lower bounds (for $\Omega$-bound) early and frequently. This may be easier because upper/lower bounds are easier to sum.

### Example - Strategy 2




$$
S_1(n) \le \sum_{i=1}^{n}\sum_{j=i}^{n}1 = n^2\text{ (*)}\\
\begin{align}
S_1(n) &\ge \sum_{i=1}^{\frac{n}{2}}\sum_{j=i}^{n}1\\
&\ge \sum_{i=1}^{\frac{n}{2}}\sum_{j = \frac{n}{2}}^{n}\\
&\ge \sum_{i =1}^{\frac{n}{2}}\frac{n}{2}\\
&= (\frac{n}{2})^2\\
&= \frac{n^2}{4}\text{ (**)}
\end{align}
$$
($*$) and ($**$) prove that $S_1(n) \in \Theta(n^2)$, and so $T_1(n) \in \Theta(n)$

### Example

```pseudocode
Test2(A, n)
	max <- 0
	for i <- 1 to n do
		for j <- i to n do
			sum <- 0
				for k<- i to j do
					sum <- A[k]
	return max
```

Let $T_2(n)$ be the runtime of `Test2(A, n)`​

Then, $T_2(n) \in \Theta(S_2(n))$, where $S_2(n)$ is the number of times we go through step 6
$$
S_2(n) = \sum_{i=1}^{n}\sum_{j=i}^{n}\sum_{k=i}^{j}1
$$

#### Strategy 1

Work out that
$$
\begin{align}
S_2(n) &= \frac{1}{12}(n-1)n(2n-1) + \frac{1}{4}(n-1)(n)+\frac{n^2}{2}+\frac{n}{2}\\
&= \frac{1}{6}n^3 + \text{lower order terms} \in \Theta(n^3)
\end{align}
$$
Then $T_2(n) \in \Theta(n^3)$

#### Strategy 2

$$
\begin{align}
S_2(n) &= \sum_{i=1}^{n}\sum_{j=i}^{n}\sum_{k=i}^{j}1\\
&\le \sum_{i=1}^{n}\sum_{j=1}^{n}\sum_{k=1}^{n}1\\
&= n^3\text{ (*)}\\
S_2(n) &\ge \sum_{i=1}^{\frac{n}{3}}\sum_{j=i}^{n}\sum_{k=i}^{j}1\\
&\ge \sum_{i=1}^{\frac{n}{3}}\sum_{j=\frac{2n}{3}}^{n}\sum_{k=i}^{j}1\\
\text{Then, } S_2(n) &\ge \sum_{i=1}^{\frac{n}{3}}\sum_{j=\frac{2n}{3}}^{n}\frac{n}{3}\\
&= \sum_{i= 1}^{\frac{n}{3}}(\frac{n}{3})^2\\
&= (\frac{n}{3})^3\text{ (**)}
\end{align}
$$

$(*)$ and $(**)$ prove that $S_2(n) \in \Theta(n^3)$. And so $T_2(n) \in \Theta(n^3)$

### Example

```pseudocode
Test3(A, n)
A: array of size n
	for i <- i to n - 1 do
		j <- i
		while j > 0 and A[j] > a[j - 1] do
			swap A[j] and A[j-1]
			j <- j-1
```

`Test3(A, n)` sorts A in descending order

##### Worst case:

`A` sorted in increasing order

 Then for all `i`, `A[i]` goes to index 0 in `i` steps. So worst case runtime $\Theta(\sum_{i=i}^{n}i) = \Theta(n^2)$

##### Best Case:

`A` sorted in decreasing order

Then, for all `i`, we exit the while loop immediately. So best case runtime $\Theta(\sum_{i=1}^{n}1) = \Theta(n)$



