# Lecture 6

CS 241 - May 23, 2019

---

## Formal Languages

> **Alphabet**: non-empty finite set of symbols, denoted $\Sigma$.
>
> **String** (or **word**): finite sequence of symbols from $\Sigma$.
>
> **Language**: set of strings.

### String

- Denote the length of a string $w$ by $|w|$
- Empty string is denoted by $\epsilon$, $|\epsilon = 0|$, $|\epsilon\epsilon\epsilon| = 0$
- Note: $\epsilon$ is not a symbol in $\Sigma$

### Example

$\epsilon$ is the empty string.

Given $\Sigma = \{a\}$, is $a = \epsilon a\epsilon$? **Yes**



$L = \{$ English words $\}$, $\Sigma = \{a,b,…,z\}$

$L = \{$ valid C Programs $\}$, $\Sigma = \{$ASCII characters$\}$

### Methods of Recognition

- Write a program (based on the language) to check.
- Other - finite state machines, Turing machines, etc.

#### Example

Given language $L = \{\text{bat, bag, bit}\}$.

We could write a program to check if a given input string matches one of the words within the language.

```pseudocode
if s[0] is 'b'
	if s[1] is 'a'
		if s[2] is in ['t','g']
			if no more input
				Accept
			else
				Reject
```

## Regular Languages

- Finite languages
- Union: $L_1 \cup L_2 = \{x : x \in L_1 \text{ or } x \in L_2\}$

- Concatenation: $L_1 L_2 = \{xy : x \in L_1, y \in L_2\}$
- Cleene star: $L^* = \{\epsilon\}\cup\{xy : x \in L^*, y \in L\} = \bigcup_{n=0}^{\infty}L^n$, where

$$
L^n =
\begin{cases}
\{\epsilon\}&\text{if }n = 0\\
LL^{n-1}&\text{otherwise}
\end{cases}
$$

Equivalently, $L^*$ is the set of all strings consisting of 0 or more occurrences of strings from *L* concatenated together.

$*$ - think "0 or more"

### Example

$L = \{a^{2n}b, n\ge 0\},\Sigma=\{a,b\}$

Is this regular?

$\{a^{2n}\}\cdot\{b\}$

$\{aa\}^*\{b\}$

### Regular Expressions

> A **regular expression* $E$, describes a regular language. We say the language of $E, L(E)$.
>
> - set of strings described by the expression

Is C Regular?

What is $\Sigma$

- ASCII
- C Tokens

Is $\{\text{C tokens}\}$ regular? Yes.

## Deterministic Finite Automata (DFA)

> Formally a **DFA** $M$ is a 5-tupple $M = (\Sigma, Q, q_0, A, \delta)$
>
> - $\Sigma$: non-empty, finite alphabet
> - $Q$: non-empty, finite set of states
> - $q_0$: start state
> - $A \subseteq Q$: set of accepting states
> - $\delta : (Q \times\Sigma) \rightarrow Q$ : transition function

### Extended Transition Function

> Can extend $\delta$ to consume a word (instead of a single symbol) by the following recursive definition:
>
> Base Case: $\delta^*(q,\epsilon)=q$
>
> Recursive Case: $\delta^*(q,cw)= \delta^*(\delta(q,c),w)\text{ where }c \in \Sigma, w \in \Sigma$

**Acceptance**: DFA $M$ accepts a word $w$ if $\delta^*(q_0,w)\in A$; i.e. accept if starting at $q_0$ and following $\delta$ for each symbol of $w$, in turn, ends in a state in $A$.

The language (set) of $M$ is all words accepted by $M$, denoted by $L(M)$.

