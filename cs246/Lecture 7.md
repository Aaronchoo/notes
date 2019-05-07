# Lecture 7
#UW/f18/cs246

## Dynamic Memory Allocation
### C
```c
int *p = malloc(___ * sizeof(int));
...
free(p)
```
**Don’t use these in C++!**

### C++
`new`/`delete`
* type-aware
* less error prone
e.g.
```cpp
struct Node {
	int data;
	Node *next;
};

Node *np = new Node;
...
delete np;
```
Variable `np` is on the stack pointing at a `Node` structure on the heap
All local variables reside on the stack
Variables on the stack are deallocated when they go out of scope, at which point the stack is popped
Allocated memory resides on the heap, remains allocated until `delete` is called
If you don’t `delete` all allocated memory, then a memory leak occurs, program will eventually fail (**an incorrect program**)
- - - -
## Allocating Arrays on the Heap
```cpp
Node *nodeArray = new Node[10];
...
delete [] nodeArray; // square brackets before array
```
Memory allocated with `new` must be deallocated with `delete`
Memory allocated with `new […]` must be deallocated with `delete []`
Mixing these is undefined behaviour 
- - - -
## Returning by value/pointer/reference
```cpp
// Expensive - n is copied to the caller's stack frame on return
// return a ptr or ref instead?
Node getMeANode() {
	Node n;
	return n;
}

// Bad - returns a pointer to stack allocated data, which is dead on return
Node *getMdeANode() {
	Node n;
	return &n;
}

// OK - returns a pointer to heap data, which is still alive
// but don't forget to delete it when done with it
Node *getMeANode() {
	Node *np = new Node;
	return np;
}
```
Which should you pick? **Return by value** Not as expensive as it looks (we’ll find out why later)
- - - -
## Operator Overloading
Give meanings to c++ operators for types we create
e.g.
```cpp
struct Vec {
	int x, y;
};

Vec operator+(const Vec &v1, const Vec &v2) {
	Vec v { v1.x + v2.x, v1.y + v2.y };
	return v;
}

Vec operator*(const int k, const Vec &v) {
	return { k * v.x, k * v.y }; // OK because compiler knows return type is Vec
}

Vec operator*(const vec &v, const int k) {
	return k * v; // reuse the above function
}
```

### Special case: overloading << and >>
e.g.
```cpp
struct Grade {
	int theGrade;
};

ostream &operator<<(ostream &out, const Grade &g) {
	out << g.theGrade << '%';
	return out;
}

istream &operator>>(istream &in, Grade &g) {
	in >> g.theGrade;
	if (g.theGrade < 0) g.theGrade = 0;
	if (g.theGrade > 100) g.theGrade = 100;
	return in;
}
```
- - - -
## The Preprocessor
Transforms the program before the compiler sees it.
`#...` means a preprocessor directive
e.g. `#include`

### Including old C headers
new naming convention
e.g. instead of `#include <stdio.h>`
use `#include <cstdio>`

### Preprocessor variables
`#define VAR VALUE` 
* sets a preprocessor variable
* then all occurrences of `VAR` is in the source file are replaced by `VALUE`
* e.g.
```cpp
#define MAX 10
int x[MAX] // transformed by the preprocessor to int x[10]
```
* use a `const` definition instead

`#define FLAG`
* sets the variable `FLAG`
* value is the empty string (nothing)

### Defined constants useful for conditional compilation
e.g.
```cpp
#define SECURITYLEVEL 1 // (or 2)
#if SECURITYLEVEL == 1
	short int // Removed if SECURITYLEVEL != 1
#elif SECURITYLEVEL == 2
	long long int // Removed if SECURITYLEVEL != 2
#endif
	publicKey;
```

### Special case
```cpp
#if 0 // never true, all inner text is removed before it reaches the compiler
...
#endif
```
heavy-duty “comment out” (and `#if`’s nest, comments don’t)

### Define symbols via compiler arguments
```cpp
// define.cc
int main() {
	cout << x << endl;
}
```
```shell
g++14 -DX=15 define.cc -o define
./define
15
```

### Debugging
```cpp
#ifdef NAME // true if NAME has been defined
#ifndef NAME // true if NAME has NOT been defined

int main() {
	#ifdef DEBUG
		cerr << "setting x=1 \n";
	#endif
	int x = 1;
	while (x < 10) {
		++x;
		#ifdef DEBUG
			cerr << "x is now" << endl;
		#endif
	}
}
```