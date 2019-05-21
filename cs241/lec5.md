# Lecture 5

CS 241 - May 21, 2019

---

## 2-Pass Assembler

### Pass 1

- Group tokens into instructions and validate
- Keep track of the memory address of each instruction
- Build a symbol table for (label, address) pair
- Note: Multiple labels may have the same address

### Pass 2

- Translate instructions into machine code
- Look up label address and compute branch offset

## Encoding Instructions into Binary

#### Example

`bne $2, $0, top`

`bne` -> 000101

`$2` -> 00010

`$0`​ -> 00000

`top` = -3 (16bits) -> 1111 1111 1111 1101

`bne $2, $0, top` -> 0001 0100 0100 0000 1111 1111 1111 1101

### Output Binary

```cpp
int instr = 5; // 0000 ... 0000 0101
// we want 0001 0100 0000 ... 0000, we left shift 32 - 6 = 26 bits
// to comebine take bitwise OR
instr |= 5 << 26;
// register $2
instr |= 2 << 21;
// register $0
instr |= 0 << 16;
int offset = -3; // 1111 .... 1101
// we want 0000 ... 1111 1111 1111 1101
// do a bitwise AND with 0000 0000 0000 0000 1111 1111 1111 1111 (0xFFFF)
instr |= offset
// put it into a char so only 1 byte gets output
// c only gets the least significant byte, so shift the bits over
char c = instr >> 24;
cout << c;
c = instr >> 16;
cout << c;
c = instr >> 8;
cout << c;
c = instr;
cout << c;
```

---

## Formal Language

### Assembly Language

- simple structure
- recognition: easy
- translation: 1 to 1 to ML

### High-level Languages

- more complex structure
- recognition: more difficult
- translation: to ML -> usually many

How do we handle this complexity?

- want a formal theory of string recognition