# Lecture 4

CS 241 - May 16, 2019

## Storing Stack

A loader allocates a block a memory and loads the program at the top of the block, then it sets $30 to the memory address immeidately following the allocated block



### Example

if `f` calls `g` and `g` calls `h` then,

`f` stores register data at the bottom of the stack,

`g` stores register data above `f`,

`h` stores register data at the top.

Register $30 stores the top of the stack, so the addresses of `f` then `g` then `h`

```assembly
f:
sw $1, -4($30)
sw $2, -8($30)
lis $1
word -8
sub $30, $30, $1
; body of procedure
add $30, $30, $1 ; assuming $1 is still 8
lw $2, -8($30)
lw $1, -4($30)
jr $31 ; return to caller
```

### Returning - jalr

Copies PC into $31 then jumps to address stored in \$s

```assembly
main:
lis $1
.word f
sw $31, -4($30)
lis $4
.word -4
add $30, $30, $4
jalr $1
sub $30, $30, $4
lw $31, -4($30)
jr $31
```

## Printing to Stdout

Use `sw` to store a word in address `0xffff000c`

least significant byte will be printed to `stdout`

## Reading from Stdin

use `lw` to read a word from address `0xffff0004`

Least significant byte will be read from `stdin`



