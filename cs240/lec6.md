# Lecture 6

CS 240 - May 23, 2019

## Building Heaps

### Problem

Given $n$ items all at once, build a heap containing all of them

### Solution #1 - Use fix-ups

Start with an empty heap and insert items one at a time.

```pseudocode
simpleHeapBuilding(A)
A: an array
	initialize H as an empty heap
	for i <- 0 to size(A)-1 do
		H.insert(A[i])
```

This corresponds to doing *fix-ups*

Worst case running time: $\Theta(n\log n)$

### Solution #2 - Use fix-downs

Using *fix-downs* instead

```pseudocode
heapify(A)
A: an array
	n <- A.size()
	for i <- parent(last(n)) downto 0 do
		fix-down(A, n, i)
```

A careful analysis yields a worst-case complexity of $\Theta(n)$

A heap can be built in linear time.

#### Recursive `heapify(T,n)`

`T` = binary tree

`n` = number elements in `T`

```pseudocode
recursive_heapify(T,n)
	if n = 1, return
	recursive_heapify(left child of T, # elements in left child)
	recursive_heapify(right child of T, # of elements in right child)
	fix-down(T, n, 0)
```

Let $t(n)$ be the runtime of `recursive_heapify`

$t(1) = c$

$t(n) = 2t(\frac{n}{2})+c\log n$

Module 1 shows that $t(n) \in \Theta(n)$

#### Proof

##### Claim

For $n$ a power of 2, $t(n) = (3n-\log n -2)c$

##### Base Case

For $n = 1, t(1) = (3 - 0 - 2)c = 0$

##### Inductive Hypothesis

Supposed that the claim is true for some $n$, a power of 2, then we prove it for $2n$

##### Inductive Step

$$
\begin{align}
	t(2n) &= 2t(n)+clog(2n)\\
	&=2(3n-\log n -2)c + c\log(2n)\text{ from IH}\\
	&=[3(2n)-2\log n-4+\log(2n)]c\\
	\\
	\log(n) &= \log(2n)-1\\
	\\
	t(2n) &= [3(2n)-2(\log(2n)-1)-4+\log(2n)]c\\
	&= [3(2n)-\log(2n)-2]c
\end{align}
$$

## HeapSort

Idea: PQ-sort with heaps.

But: Use same input-array A for storing heap.

```pseudocode
HeapSort(A, n)
	// heapify
	n <- A.size()
	for i <- parent(last(n)) downto 0 do
		fix-down(A, n, i)
	
	// repeatly find maximum
	while n > 1
		// delete the maximum
		swap items at A[root()] and A[last()]
		decrease n
		fix-down(A, n, root())
```

The for loop takes $\Theta(n)$ time and the while loop takes $O(n\log n)$ time.

## Heap Summary

> **Binary heap**: A binary tree that satisfies structural property and heap-order property

- Heaps are one possible realization of ADT PriorityQueue:
  - insert takes $O(\log n)$
  - deleteMax takes $O(\log n)$
  - also supports findMax in $O(1)$
- A binary heap can be built in linear time
- PQ-sort with binary heap leads to a sorting algorithm with $O(n\log n)$ worst case run time

## Finding the largest items

### Problem

Find the $k$th largest item in an array $A$ of $n$ distinct numbers

### Solution 1

Make k passes through the array, deleting the maximum each time

Complexity: $\Theta(kn)$

### Solution 2

Sort $A$, then return $A[n-k]$

Complexity: $\Theta(n\log n)$

### Solution 3

Scan the array and maintain the $k$ largest numbers seen so far in a min heap

```pseudocode
for n in A:
	if heap.size() < k:
		heap.insert(n)
	else if n > heap.min():
		swap(n, heap.min())
		fix-down(heap, heap.size(), 0)
```

Complexity: $\Theta(n \log k)$

### Solution 4

Create a max heap with `heapify(A)`. Call `deleteMax(A)` $k$ times

Complexity: $\Theta(n + k\log n)$

### Comparing #3 and #4

#### Claim

$f(x) = \frac{x}{\log x}$ is an increasing function (for $x \ge 3$)

#### Proof

$$
\begin{align}
	f(k)=\frac{k}{\log k} &\le f(n) = \frac{n}{\log n}\\
	\implies k\log(n) &\le n\log(k)
\end{align}
$$

Therefore solution #4 is the better solution

## Selection vs Sorting

The *selection problem*: Given an array $A$ of $n$ numbers, and $0 \le k \le n$, find the element that would be at position $k$ of the sorted array.



Best heap-based algorithm had running time $\Theta(n+ k\log n)$

For *median finding* (selection with $k = \lfloor\frac{n}{2}\rfloor$) this is $\Theta(n \log n)$

This is the same cost as our best sorting algorithm



**Question**: can we do selectiong sort in linear time?

The *quick-select* algorithm answers this question in the affirmative.

The encountered sub-routines will also be useful for other sorting algorithms.