# Lecture 13

CS 241 - June 18, 2019



## LL(1)

> A grammar is **LL(1)** if 
>
> - no two distinct productions with the same left hand side can generate the same first terminal symbol
> - no nullable symbol $A$ has the same terminal symbol in both its First and Follow sets
>   - e.g. TOS: $A$, next input: $a$, do we use $A \Rightarrow a$ or $A\rightarrow \epsilon … B \Rightarrow aB$
> - there is only one way to send a nullable symbol to $\epsilon$

### Example

$E \rightarrow E + T \mid T$

$T \rightarrow T * F \mid F$

$F \rightarrow a\mid b \mid c \mid (E)$

- operator precedence
- left associativity
- unambiguous

Is this $LL(1)$? No.

TOS: $E$, next input is: $a$ which rule to apply

$E \Rightarrow E+T \Rightarrow T+T \Rightarrow F+T \Rightarrow a+T$

$E \Rightarrow T \Rightarrow F \Rightarrow a$

2 paths to generate terminal $a$ next.

Two derivations, same first symbol.

Left recursion always not $LL(1)$



$E \rightarrow T + E \mid T$

$T \rightarrow F * T \mid F$

$F \rightarrow a\mid b \mid c \mid (E)$

- make it right recursive
- also not $LL(1)$



Need to factor

$E \rightarrow TE'$

$E' \rightarrow \epsilon \mid + E$

$T \rightarrow FT'$

$T' \rightarrow \epsilon \mid * T$

$F \rightarrow a \mid b \mid c$

This is $LL(1)$

- right associative



## Bottom-Up Parsing

- go from $w$ to $s$

Stack stores partially reduced info read so far

$w \Leftarrow \alpha_k … \Leftarrow \alpha_1 \Leftarrow S$

Invariant: stack + entire unread input = $\alpha_i$ (or $w$ or $s$)

### Example

$S' \rightarrow \vdash S \dashv$

$S \rightarrow AyB$

$A \rightarrow ab$

$A \rightarrow cd$

$B \rightarrow Z$

$B \rightarrow wx$



input: $\vdash abywx \dashv$

choice at each step:

1. Shift symbol from input to stack
2. Reduce - TOS in the RHS of a grammar rule replace with LHS

| Stack             | Read                 | Unread               | Action                                                |
| ----------------- | -------------------- | -------------------- | ----------------------------------------------------- |
|                   |                      | $\vdash abywx\dashv$ | Shift $\vdash$                                        |
| $\vdash$          | $\vdash$             | $abywx\dashv$        | Shift $a$                                             |
| $\vdash a$        | $\vdash a$           | $bywx\dashv$         | Shift $b$                                             |
| $\vdash a b$      | $\vdash a b$         | $ywx\dashv$          | Reduce $A \rightarrow ab$: pop $b$, pop $a$, push $A$ |
| $\vdash A$        | $\vdash a b$         | $ywx\dashv$          | Shift $y$                                             |
| $\vdash Ay$       | $\vdash aby$         | $wx\dashv$           | Shift $w$                                             |
| $\vdash Ayw$      | $\vdash abyw$        | $x\dashv$            | Shift $x$                                             |
| $\vdash Aywx$     | $\vdash abywx$       | $\dashv$             | Reduce $B \rightarrow wx$: pop $x$, pop $w$, push $B$ |
| $\vdash AyB$      | $\vdash abywx$       | $\dashv$             | Reduce $S \rightarrow AyB$                            |
| $\vdash S$        | $\vdash abywx$       | $\dashv$             | Shift $\dashv$                                        |
| $\vdash S \dashv$ | $\vdash abywx\dashv$ |                      | Reduce $S \rightarrow\ \vdash S \dashv$               |
| $S'$              | $\vdash abywx\dashv$ |                      | Accept                                                |

Accept if stack contains $S'$ when input is $\epsilon$ 

Equivalent: $\vdash S \dashv$ on empty input, accept when machine shifts $\dashv$



How do we know when to shift or reduce?

- use next symbol of input
- problem is still hard



### Theorem (Donal Knuth, 1965)

> The set $\{wa \mid \exists x, S \Rightarrow^* wax\}$ ($w$ is stack, $a$ is next input symbol) is a Regular Language

Use a DFA to make shift/reduce decisions.

Resulting method is $LR$

- Left-to-right scan of input
- Rightmost derivation



### Example

$S' \rightarrow\ \vdash E \dashv$

$E \rightarrow E + T$

$E \rightarrow T$

$T \rightarrow id$



### Definition

> an item is a production with a dot, $\bullet$, somewhere on the RHS (indicates partially completed rules)

- label a transition with the symbol that follows the dot, advance dot in next state

- If the dot precedes a non-terminal $A$, add all productions with $A$ on LHS to state and dot in the leftmost position.