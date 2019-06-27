# Lecture 1

CS 240 - May 7, 2019

---

## Introduction

### Mark Breakdown

- Final $50\%$
- Midterm $25\%$
- Assignments $25\%$

## Asymptotic Analysis

### Problems (terminology)

#### Problem

count positive integers in an array

#### Problem Instance

An instance could be `[-5,10,-5,2]`

#### Problem Solution

The solution is 2

#### Size of a problem instance

Size of the input is the length of the array

### Algorithms and Programs

#### Algorithm

```python
def count(arr):
    res = 0
    for num in arr:
        if num > 0:
            res += 1
    return res
```

### Order Notation

#### O-Notation

$f(n) \in O(g(n))$ if there exist constants $c > 0$ and $n_0 > 0$ such that $|f(n)| \le c|g(n)|$ for all $n \ge n_0$

#### Example of Order Notation

$f(n) = 2n^2 + 3n + 11$
$g(n) = n^2$

For $n \ge 1$, $2n^2 \le 2n^2$ 

​		$3n \le 3n^2$

​		$11 \le 11n^2$

$f(n) \le 16n^2$

Taking $c = 16$ and $n_0 = 1$, this proves that $f(n) \in O(n^2)$

