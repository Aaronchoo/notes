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

