#Lecture 12

CS 241 - June 13, 2019

## Parsing

$\text{Predict}(A,a) = \{a \rightarrow  \beta \mid a \in \text{First}(\beta)\}$ not quite correct

- $\text{First}(\beta) = \{a \mid \beta \Rightarrow^* a\delta\}$ where $a \in \Sigma, \delta \in V^*$
- Need to consider non-terminal that are Nullable; i.e. $A \Rightarrow^* \epsilon$

$\implies \text{Predict}(A,a) = \{A \rightarrow  \beta \mid a \in \text{First}(\beta)\} \cup \{A \rightarrow \beta \mid \text{Nullable}(\beta), a \in \text{Follow}(A)\}$



$\text{Follow}(A) = \{b \mid S' \Rightarrow^* \alpha Ab\delta\}$

- terminals that can come immediately after A in a derivation starting from S'

### Example

$S \rightarrow AB$

$A \rightarrow a$

$A \rightarrow \epsilon$

$B \rightarrow b$



$\text{Predict}(A,b) \Rightarrow \text{First}(A) = \{a\}$ A is nullable

$\text{First}(B) = \{b\}$



$s \Rightarrow AB \Rightarrow Ab$

$\text{Follow}(A)=\{b\}$



### Computing Nullable

Initialize: $\text{Nullable}[A] = \text{false}, \forall A \in N$, where $N$ is the set of non-terminals

repeat for each rule $B \rightarrow B_1 … B_k$, $B_i \in V = \Sigma \cup N$

if $k = 0$ or $\text{Nullable}[B_1] = … = \text{Nullable}[B_k] = \text{true}$

$\text{Nullable}[B] = \text{true}$

Until nothing happens



### Computing First

```pseudocode
Initialize: First[A] = {}, for all A in N

for each rule A -> B_1 to B_k
	for i = 1 to k
		if B_i is a terminal a
			First[A] += {a}
			break
		else
			First[A] += First[B_i]
			if not Nullabble[B_i]
				break
until nothing changes
```



Computing $\text{First}^*(\beta)$, $\beta \in V^*$ "first set" for a string of symbols ($V^*$)

```pseudocode
First*(Y_1 to Y_n) where Y_i is in V
	result <- NIL
	for i = 1 to n
		if y_i is not in Sigma // non-terminal
			result += First[Y_i]
			if not Nullable[Y_i]
				break
		else // terminal
			result += {Y_i}
			break
	return result
```



### Computing Follow

```pseudocode
Initialize: Follow[A] = {} for all A not in S_prime

for each rule A -> B_1 to B_n
	for i = 1 to n
		if B_i is in N  // only do follow sets for non-terminal
			Follow[B_i] += First*(B_i+1 to B_n)
			if all of B_i+1 to B_n are nullable
				Follow[B_i] += Follow[A]
until nothing changes
```

