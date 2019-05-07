## Lecture 1

May 7, 2019

---

### Assignments

10 assignments - most are independent

A3/A4: assembler

### Sequential Programs

no concurrency, parallel, or multi-threading

goal: understanding how sequential programs work

### Starting Point

- bare hardware
- MIPS architecture
- MIPS machine language ($0$'s and $1$'s)
- MIPS assembly language - English-like ML

_at the end, get $C$-like programs to run on MIPS_

### Binary Strings

`1010`

what does this mean?

- `10`: unsigned binary
- `2`: "sign-magnitude" binary
- `6`: two's complement
- `newline`: ASCII
- `gray`: grayscale

### How do computers know what representation/interpretation to use?

- Files
  - file extensions (e.g. .docx .jpeg)
  - header
- Programming
  - type declarations
  - Can you change how bits are interpreted? Yes, casting

---

### Basics

__bit__ - one binary digit (0 or 1)

__byte__ - 8-bits

__nibble__ - 4-bits

__word__ - today in the real world 64-bit architecture, 32-bit architecture for $CS\ 241$

---

### Number Systems

#### Decimal (base 10)

digits 0 - 9

$1239 = 1 * 10^3 + 2 * 10^2 + 3 * 10^1 + 9 * 10^0$

#### Binary (base 2)

digits 0, 1

$11001001$

unsigned binary: $1 * 2^7 + 1 * 2^6 + 1 * 2^3 + 1 * 2^0 = 201$

##### converting decimal to binary

$201 - 128 = 73$: 1

$73 - 64 = 9$: 1

$9 - 32 < 0$: 0

$9 - 16 < 0$: 0

$9 - 8 = 1$: 1

$1 - 4 < 0$: 0

$1 - 2 < 0$: 0

$1 - 1 = 0$: 1

_hence `11001001`_

##### how do we represent negative numbers?

use first bit as a sign bit

- 1 = negative
- 0 = positive

$11001001$

$64 + 8 + 1 = 73 => -73$ in "sign-magnitude" binary

###### problems

- 2 zeroes: positive and negative, wasteful
- add/sub is complicated

#### Two's Complement

#### Method 1

1. Intepret the n-bit number as an unsigned integer
2. If the first bit is 0, done
3. Else subtract $2^n$

e.g. $n = 3$ each bit is 0 or 1

$000: 0,\ 001: 1,\ 010: 2,\ 011: 3,\ 100: -4,\ 101: -3,\ 110: -2,\ 111: -1$

- still use first bbit to determine sign
- only 1 representatino for $0$
- addition is simple: regular add mod $2^n$

#### Method 2

positive numbers are "normal" binary magnitude

if negative

1. start with magnitude of the number
2. flip bits, 1 => 0, 0 => 1
3. add 1

##### example: -73 to 8-bit 2's complement:

1. 01001001 (magnitude)
2. 10110110 (flip bits)
3. 10110111 (-73 in 2's complement)

##### how do we convert from 2's complement?

do reverse of process __OR__ do process again

##### example: show the two are the same

convenience: shorter notation - use hexadecimal

- base 16, $2^4$
- digits: 0 ... 9, A, B, C, D, E
- $10010111\ =\ B7$