# Lecture 15

CS 240 - June 27,2019

## Multiway Tries

- Operations `search(x)`, `insert(x)`, `delete(x)` are exactly as for tries for bitstrings
- Run-time $O(|x|\cdot(\text{time to find the appropriate field}))$

Each node now has up to $|\Sigma|+1$ children. How should they be stored?

### Solution 1

Array of size $|\Sigma|+1$ for each node.

Complexity: $O(1)$ time to find child, $O(|\Sigma|n)$ space.

### Solution 2

List of children of each node

Complexity: $O(|\Sigma|)$ time to find child, $O(\text{# children})$ space.

### Solution 3

Dictionary of children for each node

Complexity: $O(\log(\text{# children}))$ time, $O(\text{# children})$ space.

Best in theory, but not worth it in practise unless $|\Sigma|$ is huge.



In practise, use hashing (keys are in typicall small range $\Sigma$)



## Dictioanry w/ Hashing

### Direct Addressing

**Special Situation:** For a known $M \in \N$, every key $k$ is an integer with $0 \le k < M$.

We can then implement a dictioanry easily: Use an array A of size M that stores $(k,v)$ via $A[k] <- v$.

Each operation is $\Theta(1)$

Total space is $\Theta(M)$

What sorting algorithm does this remind you of? *Count Sort*

### Hashing

Two disadvantages of direct addressing

- It cannot be used if the keys are not integers
- It wastes space if $M$ is unkown or $n << M$

**Hashing Idea:** Map keys to integers in range $\{0…M-1\}$ and then use direct addressing

Details

- **Assumption:** We know that all keys come from some **universe** $U$. (typically $U = \N$)
- We design a hash function $h : U \rightarrow \{0…M-1\}$
  - commonly used: $h(k) = k \mod M$
- Store dictionary in **hash table**, i.e. an array $T$ of size $M$
- An item with key $k$ should idealy be stored in **slot** $h(k)$, i.e. at $T[h(k)]$

Division with remainder:

given $M > 0$, any $k \ge 0$ can be written as

$k = qM + r$, $r \in \{0… M-1\}$ (written $r = k \mod M$)

### Collisions

- Generally hash function $h$ is not injective, so many keys can map to the same integer
- We get **collisions**: we want to insert $(k,v)$ into the table but $T[h(k)]$ is already occupied

### Separate Chaining

Simpliest collision-resolution strategy: Each slot stores a **bucket** containing 0 or more KVPs

- A bucket could be implemented by any dictionary realization (even another hash table)
- The simplest approach is to use unsorted linked lists for budkets. This is called collision resolution by separate chaining.
- $serach(k)$: Look for key $k$ in the list at $T[h(k)]$. Apply MTF heuristic
- $insert(k,v)$: Add $(k,v)$ to the front of the list at $T[h(k)]$
- $delete(k)$: Perform a search, then delete from the linked list.

### Complexity of chaining

Run-times: $insert$ takes time $O(1)$

$search$ and $delete$ have run-time $O(1 + \text{ size of bucket }T[h(k)])$

- the average bucket size is $\alpha = \frac{n}{M}$ = $\frac{\text{# keys in the table}}{\text{length of the table}}$
- However, this does not imply the average-case cost of $search$ and $delete$ is $O(1+ \alpha)$
- **Uniform Hashing Assumption:** Each hash function value is equally likely
- Under this assumption, each key collides is expected to collide with $\frac{n-1}{M}$ other keys and the average-case cost of $search$ and $delete$ is hence $O(1+ \alpha)$

### Uniform hashing assumption

Suppose we hash keys $k_1 … k_n$ into a table of length $M$.

#### 

For any key$k$ value $a \in \{0…M-1\}$

$Prob(h(k)=a) = \frac{1}{M}$



For any keys $k_i, k_j$ values $a, b$

$Prob(h(k_i)=a and h(k_j)=b) = Prob(h(k_i)=a)\times Prob(h(k_j)=b) = \frac{1}{M^2}$

### Under UHA

For any keys $k_i, k_j$,
$$
\begin{align}
&Prob(h(k_i) = h(k_j))\\
&= Prob(h(k_i)=h(k_j)=0)+Prob(h(k_i)=h(k_j)=1)+...+Prob(h(k_i)=h(k_j)=M-1)\\
&= \frac{1}{M^2} + ... + \frac{1}{M^2}\\
&= \frac{1}{M^2} \times M\\
&= \frac{1}{M}
\end{align}
$$

$$
X_{ij}=
\begin{cases}
1 &\text{if }h(k_i)=h(k_j)\\
0 &\text{otherwise}
\end{cases}
$$

Number of collisions involving $k_i = \Sigma_{j \ne i} X_{ij}$ 

$E[\text{# collisions involving k_i}] = \Sigma_{j \ne i}E[X_{ij}]=\Sigma_{j\ne i}\frac{1}{M}=\frac{n-1}{M} \approx \alpha$

### Load factor and re-hashing

- For all collision resolution strategies, the run-time evaluation is done in terms of the load factor $\alpha = \frac{n}{M}$
- We keep the load factor small by rehashing when needed:
  - keep track of $n$ and $M$ throughout operations
  - if $\alpha$ gets too large, create new (twice as big) hash table, new hash function(s) and re-insert all items in the new table
- Rehashing costs $\Theta(M+n)$ but happens rarely enought that we can ignore this term when amortizing over all operations.
- We should also re-hash when $\alpha$ gets too small, so that $M \in \Theta(n)$ throughout, and the sapce is always $\Theta(n)$.

**Summary:** If we maintain $\alpha \in \Theta(1)$, then (under UHA) the average cost for hashing with chaining is $O(1)$ and the space is $\theta(n)$

### Open addressing

**Main Idea:** Avoid the links needed for chaining by permitting only one item per slot, but allowing a key $k$ to be in multiple slots.

$serach$ and $insert$ follow a **probe sequence**  of possible locations for key $k$: until an empty spot is found.

$delete$ becomes problematic

- cannot leave an empty spot behind; the next search might otherwise not go far enough
- idea 1: move later items in the probe sequence forward
- idea 2: lazy deletion: mark spot as deleted and continue searching past deleted spots.