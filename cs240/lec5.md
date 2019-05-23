# Lecture 5

CS 240 - May 21, 2019

---

## Realizations of Priority Queues

### Realization 1 - Unsorted Arrays

- insert: $O(1)$
- deleteMax: O(n)

- insert(A, k)
  - if A is full, double its size, then copy k into A

- cost of insert: if length(A) = n

  - 1, copy if A is not full

  - 1 + n otherwise

Supposed we start with length(A) = 1

Total cost of n inserts (n is a power of 2) is

$1 + … + 1 + (1+2+4+8+16+…+n) = 3n-1$

PQ-sort with this realization yields selection sort

### Realization 2 - Sorted arrays

PQ using a sorted array.

- insert: $O(n)$
- deleteMax: $O(1)$

```pseudocode
A = [1,2,7]
// insert 6
A = [1,2,6,7]
```

PQ-sort with this realization yields insertion sort

### Realization 3 - Heaps

> A **(binary) heap** is a certain type of binary tree

height of a binary tree is the length of its longest path from the root to a node

> A **heap** is a binary tree with the following two properties:
>
> 1. **Structural Property**: All the levels of a heap are completely filled, except for the last level. The filled items in the last level are left-justified.
> 2. **Heap-order Property**: For any node $i$, the key of the parent of $i$ is the larger than or equal to the key of $i$
>
> The full name for this is *max-oriented binary heap*.

> **Lemma**: The height of a heap with *n* nodes is $\Theta(\log(n))$

Number of nodes in a heap of height 3:

- least number of nodes is 8
- max number of nodes is 15

$8 \le n \le 15$ if $h = 3$

$2^h \le n \le 2^{h+1}-1$ for any $h$ for any binary tree

so we get

$h \le \log(n)$ and $\log(n+1)\le h+1 \implies \log(n+1)-1 \le h$

Number of nodes in a heap of height h is at least $2^h$ and at most $2^{h+1}-1$

#### Storing Heaps in Arrays

Heaps should not be stored as binary trees!

Let H be a heap of n items and let A be an array of size n. Store root in `A[0]` and continue with elements level-by-level from top to bottom, in each level left-to-right.

```pseudocode
A = [50,24,34,27,15,...]
```

- root node is at index 0
- left child of node $i$ is node $2i+1$
- right child of node $i$ is node $2i+2$
- parent of node i is node $\lfloor\frac{i-1}{2}\rfloor$
- last node is $n-1$

#### insert in Heaps

- place the new key at the first free leaf
- perform a fix-up:

```pseudocode
fix-up(A,k)
	while parent(k) exists and A[parent(k)] < A[k] do
		swap A[parent(k)] and A[k]
		k <- parent(k)
		
```

The new item "bubbles up" until it reaches its correct place in the heap

Time: $O(h) = O(\log(n))$ where $h$ is the height of the tree

#### deleteMax in Heaps

- the max item in the heap is the root
- replace root with last leaf node
- perform a fix-down:

```pseudocode
fix-down(A, n, k)
	while k is not leaf do
		// Find child with larger key
		j <- left child of k
		if (j is not last(n) and A[j+1] > A[j])
			j <- j + 1
		if A[k] >= A[j]
			break
		swap A[k] and A[j]
		k <- j
```

Time: $O(h) = O(\log(n))$ where $h$ is the height of the tree

