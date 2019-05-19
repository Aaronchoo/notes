# Lecture 2

CS 240 - May 9, 2019

---

## Order Notation

### Example

$f(n) = 75n + 500$

$g(n) = 5n^2$

For $n \ge 20$, $20n \le n^2$

​					$100n \le 5n^2$

​					$500 \le 25n$

so if $n \ge 20$,
$$
f(n) = 500 + 75n \le 25n + 75n \le 5n^2 = g(n)
$$
Since also $f(n) \ge 0$ for all n,

taking $n_0 = 20$ and $c = 1$, this proves $f(n) \in O(g(n))$

### Another Proof

for $n \ge 1$,
$$
75n \le 75n^2​
$$

$$
500 \le 500n^2​
$$

$$
f(n) \le 575n^2 = 115 g(n)
$$

so taking $n_0 = 1,\ c = 115$, this proves $f(n) \in O(g(n))$

---

## Asymptotic Lower Bound

### $\Omega$-notation

$$
f(n) \in \Omega(g(n))
$$

if there exist $c > 0,\ n_0 > 0$ such that
$$
|f(n)| \ge c|g(n)|,\ n \ge n_0
$$

#### Example

$f(n) = 2n^2 + 3n + 11$

$g(n) = n^2$

for $n \ge 1$,
$$
2n^2 \ge 2n^2
$$

$$
3n \ge 0
$$

$$
11 \ge 0
$$

$$
f(n) \ge 2n^2 = 2g(n)
$$

Taking $n_0 = 1,\ c = 2$, this proves that $f(n) \in \Omega(g(n))$

_$n_0 = 1,\ c=1$ work as well_

#### Example

$f(n) = \frac{1}{2}n^2 - 5n$

$g(n) = n^2$

For $n \ge 20$,
$$
n^2 \ge 20n
$$

$$
\frac{1}{4}n^2 \ge 5n\ (divide\ by\ 4)
$$

$$
-5n \ge -\frac{1}{4}n^2
$$

$$
\frac{1}{2}n^2 - 5n \ge \frac{1}{2}n^2 - \frac{1}{4}n^2\ (add\ \frac{1}{2}n^2)
$$

So taking $n_0 \ge 20,\ c = \frac{1}{4}$, this proves that $f(n) \in \Omega(g(n))$

### $\Theta$-notation

$$
f(n) \in \Theta(g(n))
$$

if there exist constants $c_1, c_2 \gt 0$ and $n_0 > 0$ such that
$$
c_1|g(n)| \le |f(n)| \le c_2|g(n)|\ for\ all\ n \ge n_0
$$

#### Example

$f(n) = log_b(n)$

$g(n) = log(n)$
$$
\frac{1}{log(b)}log(n) = \frac{1}{log(b)}g(n) \le f(n) = \frac{log(n)}{log(b)} \le \frac{1}{log(b)}log(n) = \frac{1}{log(b)}g(n)
$$
Taking $n_0 = 1, c_1 = c_2 = \frac{1}{log(b)}$, this proves that $f(n) \in \Theta(g(n))$

---

## Strictly smaller/larger asymptotic bounds

### $o$-notation

$$
f(n) \in o(g(n))
$$

if for all $c > 0$, there exists $n_0$ (depending on $c$)

such that for $n \ge n_0$
$$
|f(n)| \le c|g(n)|
$$

#### Example

$f(n) = 2000n^2$

$g(n) = n^n$

Given $c>0$, we have to find $n_0$ (depend on $c$), such that for $n \ge n_0$,
$$
|f(n)| \lt c|g(n)| <=> 2000n^2 < cn^n
$$
$n$ equivalent to $2000 < cn^{n-2}$

1. For $n \ge 3, n-2\ge1$, so $n^1 \le n^{n-2}$
2. For $n \ge 3$ and $n \ge \frac{2000}{c} + 1$

$$
\frac{2000}{c} \lt \frac{2000}{c} + 1 \le n \le n^{n-2}
$$

so taking $n_0 = max(3, \frac{2000}{c} + 1)$, this proves $f(n) \in o(g(n))$

### $\omega$-notation

$$
f(n) \in \omega(g(n))
$$

if
$$
g(n) \in o(f(n))
$$

---

## Algebra of Order Notations

### Identity rule

$$
f(n) \in \Theta(f(n))
$$

### Maximum rules

Supposed that $f(n) > 0$ and $g(n) > 0$ for all $n \ge n_0$, then
$$
O(f(n) + g(n)) = O(max(f(n), g(n)))
$$

$$
\Omega(f(n) + g(n)) = \Omega(max(f(n), g(n)))
$$

---

## Techniques for Order Notation

Supposed that $f(n) > 0$ and $g(n) > 0$ for all $n \ge n_0$. Supposed that
$$
L = \lim_{n\to\infty}\frac{f(n)}{g(n)}
$$

### Example 1

Let $f(n)$ be a polynomial of degree $d > 0$.
$$
f(n) = c_dn^d + c_{d-1}n^{d-1} + ...+c_1n+c_0
$$
for some $c_d > 0$.

Let $g(n) = n^d$

Then
$$
\frac{f(n)}{g(n)} = \frac{c_dn^d + c_{d-1}n^{d-1} + ...+c_1n+c_0}{n^d}
$$

$$
= c_d + c_{d-1}\frac{1}{n}+...+c_1\frac{1}{n^{d-1}}+c_0\frac{1}{n^d}
$$
Then, $\lim_{n\to\infty} \frac{f(n)}{g(n)}$ exists, and is equal to
$$
c_d + 0 + ... + 0 + 0 = c_d \gt 0
$$
by the limit test,
$$
f(n) \in \Theta(g(n))
$$

### Example 2

$f(n) = n(2+sin(\frac{n\pi}{2}))$

$g(n) = n$

for $n \gt 1$,
$$
-1 \le sin(\frac{n\pi}{2}) \le 1
$$

$$
1 \le sin(\frac{n\pi}{2}) + 2 \le 3
$$

$$
n \le f(n) \le 3n
$$

So taking $n_0 = 1$, $c_1= 1,\ c_2 = 3$, this proves $f(n) \in \Theta(g(n))$ 

On the other hand
$$
\frac{f(n)}{g(n)} = 2 + sin(\frac{n\pi}{2})
$$
has no limit at $n = \infty$

_the limit test does not apply_