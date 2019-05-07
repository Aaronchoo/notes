# Lecture 8
#UW/f18/cs246

## Separate Compilation
Splits programs into **modules**, which provide
**interface**
	* type definitions
	* prototypes for functions
	* `.h`
**implementation**
	* full definition for all provided functions
	* `.cc`

### Recall
Declaration
	* asserts existence 
Definition
	* full details
	* allocates space for variables and functions
- - - -
### Example
interface (`vec.h`)
```cpp
struct vec {
	int x, y;
};

vec operator +(const vec &v1, const vec &v2);

// declaration
extern int globalNum;
```

`main.cc`
```cpp
#include "vec.h"

int main () {
	vec v {1, 2};
	v = v + v;
}
```


implementation `vec.cc`
```cpp
#include "vec.h"

// definition
int globalNum;

vec operator + (const vec &vi, const vec &v2) {
	return ... ;
}
```
- - - -
### Recall
An entity can be declared many times, but defined at most once.
- - - -
### Compiling Separately:
`g++14 -c vec.cc`
`g++14 -c main.cc`
`-c`
	* means compile only
	* do not link
	* do not try to build executable
	* produces an object file `.o`
`g++14 vec.o main.o -o main`
	* links the object files into an executable
	* `./main` to run
- - - -
### What happens if we change vec.cc?
Only need to recompile `vec.cc` and relink
- - - -
### What happens if we change vec.h?
Need to recompile **both** `vec.cc` and `main.cc` (they both include vec.h) and relink
- - - -
### How do we keep track of dependencies and perform minimal recompilation
**Linux Tool**: `make`
Create `Makefile` that says which files depend on which other files

``` makefile
# main depends on these
main: main.o vec.o # target
	# the indent must be a tab character
	g++ main.o vec.o -o main # recipe
main.o: main.cc vec.h # target
	g++ -std=c++14 -c main.cc # recipe
vec.o: vec.cc vec.h
	g++ -std=c++14 -c vec.cc
# optional target for removing all binary files
.PHONY: clean
clean:
	rm *.o main
```

Then from the command line run `make`, builds the whole program

Now change just `vec.cc` and run `make`, compiles `vec.cc` only, not the whole program, relinks

`make target`
	* builds the requested target (builds the first target if name specified)
`make clean` 
	* removes all binaries
`make`
	* builds main
	* What does main depend on?
		* recursively build dependencies if necessary
		* then build main if necessary
	* If a target is **older** than its dependencies (based on last modified time), it is rebuilt
		* if `vec.cc` changes, it is newer than `vec.o`, rebuild `vec.o`
			* then `vec.o` is newer than `main`, so rebuild `main`
- - - -
### Generalize Makefile with variables
``` makefile
CXX = g++ # compiler's name
CXXFLAGS = -std=c++14 -Wall -g # compiler options
OBJECTS = main.o vec.o
EXEC = main

${EXEC}: ${OBJECTS}
	${CXX} ${CXXFLAGS} ${OBJECTS} -o ${EXEC}
# can omit recipes - make guesses that the recipe is ${CXX} ${CXXFLAG}...
main.o: main.cc vec.h
vec.o: vec.cc vec.h
.PHONY: clean
clean:
	rm ${EXEC} ${OBJECTS}
```

Hardest part of writing Makefiles - writing dependencies and keeping them up to date
Can get help from g++: `g++ -MMD -c vec.cc`
	* creates `vec.o` AND `vec.d`

`vec.d vec.o: vec.cc vec.h`
	* now just include this in the Makefile

```makefile
CXX = g++ # compiler's name
CXXFLAGS = -std=c++14 -Wall -g -MMD # compiler options
OBJECTS = main.o vec.o
DEPENDS = ${OBJECT: .o=.d}
EXEC = main

${EXEC}: ${OBJECTS}
	${CXX} ${CXXFLAGS} ${OBJECTS} -o ${EXEC}

-include ${DEPENDS}

.PHONY: clean
clean:
	rm ${EXEC} ${OBJECTS} ${DEPENDS}
```

As the project expands, only add `.o` files to the Makefile

Supposed we want to write a linear algebra module (separate/example3)
```cpp
// linalg.h
#include "vec.h"
...
```

```cpp
// linalg.cc
#include "linalg.h"
#include "vec.h"
...
```

```cpp
// main.cc
#include "linalg.h"
#include "vec.h"
...
```

Won’t compile, why?
`main.cc`, `linalg.cc` each get 2 copies of `vec.h` - `struct vec` declared twice

Need to prevent files from being included more than ones
Solution:
```cpp
// vec.h
#ifndef VEC_H
#define VEC_H
... // normal file contents
#endif
```

First time `vec.h` is included, symbol `VEC_H` not defined, so file is included
Subsequently, `VEC_H` is defined, so `vec.h` contents are suppressed

**Always** put `#include`  guards in `.h` files
**Never**
	* put `using namespace std` in header files
		* forces this on all clients that include the header
		* always std:: in headers
	* compile `.h` files
	* include `.cc` files
- - - -
## Classes
### Can put functions inside structs

```cpp
// student.h
struct Student {
	int assns, mt, final;
	float grade();
};
```

```cpp
// student.cc
#include "student.h"

float Student::grade() {
	return assns * 0.4 + mt * 0.2 + final * 0.4;
}
```

```cpp
// client
Student s {60, 70, 80};
cout << s.grade() << endl;
```
- - - -
### Class - essentially a struct type that can contain functions
C++ has a class keyword - later
An **object** is an instance of a class
Function `grade` is called a **member function** (or **method**)