# Lecture 3

CS 241 - May 14, 2019

---

May 21 lecture in MC 4040

## MIPS Instructions

### Load word

#### Example

Given $1 stores the base address of an array and \$2 stores the length, load the value at index 7 into \$3

`lw $3 28($1)`

`1000 1100 0010 0011 0000 0000 0001 1100`

### Load Immediate and Skip

`lis $d`

#### Example

`lis $7`

`.word 0x7`

To execute `lis $7`, the `.word 0x7` at the current PC is loaded.

The value `0x7` will be in register `$7`

Then, `PC <— PC + 4` to perform the skip

### Branching

`beq $s, $t, i`

`bne $s, $t, i`

if `$s` and `$t` are equal/unequal, modify PC by the given offset `i`

`PC <— PC + i * 4`

#### Example

`beq $0, $0, -1` is an infinite loop

### Set Less Than

`slt $d, $s, $t`

`sltu $d, $s, $t`

Sets `$d` to 1 if `$s < $t`, otherwise sets `$d` to 0

### Examples

#### Conditional

Write a program to compute the absolute value of $1 and store the result in \$1

```assembly
slt $2, $1, $0 ; check if $1 is less than 0
beq $2, $0, 1 ;
sub $1, $0, $1 ; set $1 to 0 - $1
jr $31
```

#### Looping

Write a program that adds all even numbers from 2 to 20 (inclusive) and stores the sum in register $3

```assembly
lis $1
.word 20
lis $2
.word 2
add $3, $0, $0 ; initialize $3 to 0
add $3, $1, $3
sub $1, $1, $2 ; decrement by 2
bne $1, $0, -3
jr $31
```

### Multiplication and Division

`mult $s, $t`

`div $s, $t`

product could require 64 bits - too big for a single register

product is stored in special registers `hi:lo <-- $s * $t`

division has a quotetient (stored in `lo`) and remainder (`hi`)

unsigned versions `multu` and `divu`

`mfhi $d` and `mflo $d` move from `hi` or `lo` to the destination register

#### Example

Given $1 stores the base address of an array and \$2 stores a valid index, write a program that lods the value into \$3

```assembly
lis $4
.word 4
mult $2, $4
mflo $5
add $5, $1, $5
lw $3, 0($5)
jr $31
```

#### Example

Write a program that checks if $2 evenly divides \$1

```assembly
div $1, $2
mfhi $3
bne $3, $0, 4
lis $4
.word 1
add $3, $4, $0
beq $0, $0, 1
add $3, $0, $0
jr $31
```

---

## Assembly Language

Assembly language replaces the binary encoding of machine language

- Readability, less chance of error
- Assembler can automatically translate asm to ml
- 1 line of asm translates to 1 line of ml
- Has extra features (e.g. `.word`)
- Allows for comments and extra white space

### Labels

Assemblers allow programmers to label instructions, so programmers don't have to manually calculate jump addresses or branch offsets

#### Example

```assembly
; Label in beq instruction
slt $2, $1, $0
beq $2, $0, foo
sub $1, $0, $1
foo:
jr $31
```

```assembly
lis $1
.word 20
lis $2
.word 2
add $3, $0, $0
top:
add $3, $3, $1
sub $1, $1, $2
bne $1, 0, top
jr $31
```

`top` is assigned memory address `0x14`

Assembler computes $\frac{\texttt{top} - \texttt{PC}}{4} = \frac{\texttt{0x14} - \texttt{0x20}}{4} = -3$ in bne



