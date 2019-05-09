# Lecture 2

CS 241 - May 9, 2019

------

## Overview

High Level Program (C/C++ Programs)

—Compiler—>

Assembly Language Program (low level English-like Instructions)

—Assembler—>

Machine Language (1's and 0's)

—Loader—>

Memory (RAM) and CPU

---

## MIPS Instructions

### Add

#### Format

`add $d $s $t`

`0000 00ss ssst tttt dddd d000 0010 0000`

#### Example

`add $7, $8, $27`

7 —> 00111

8 —> 01000

27 —> 11011

`0000 0001 0001 1011 0011 1000 0010 0000` in binary

`0x011B3820` in hexadecimal 

---

