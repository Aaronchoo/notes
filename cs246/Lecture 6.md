# Lecture 6
#UW/f18/cs246

### Example - Echo all numbers, skip non numbers
```cpp
int main() {
	string s;
	while (cin >> s) {
		istringstream sock {s};
		int n;
		if (sock >> n) cout << n << endl;
	}
}
```
- - - -
## Default Function Parameters
```cpp
void printFile(string name = "file.txt") {
	ifstream f {name};
	string s;
	while (f >> s) cout << s << endl;
}

printFile("abc.txt") // uses abc.txt
printFile() // uses file.txt
```

**Note** optional parameters must be last
- - - -
## Overloading
### C
```c
int negInt(int n) { return -n; }
bool negBool(bool b) { return !b; }
```

### C++
Functions with different parameter lists can have the same name
```cpp
int neg(int n) { return -n; }
bool neg(bool n) { return !n; }
```
This is called overloading
Correct version of `neg` for each function call, is chosen by the compiler (i.e. at compile-time)
	* based on the # and types of arguments in the function call
Therefore overloads must differ in number and types of arguments
	* just differing in return type is not enough
- - - -
## Structs
```c
struct Node {
	int data;
	struct Node *next; // c++ can omit struct
}; // don't forget the semi colon
```
why is this wrong
```cpp
struct Node {
	int data;
	Node next;
};
```
Memory of Node is not allocated
- - - -
## Constants
```cpp
const int maxGrade = 100; // must be initialized
```

declare as many things `const` as you can
	* help catch errors

`Node n { 5, nullptr };`
syntax for a null ptr in c++
do not say `NULL` or `0`

`const Node n2 = n;`
immutable copy of n
- - - -
## Parameter Passing
### Recall
```cpp
void inc(int n) { ++n; }
...
int x = 5;
inc(x);
const << x << endl; // prints 5
```
this is an example of pass by value
`inc` gets a copy of x, mutates the copy, the original is unchanged

### Solution
pass a ptr
```cpp
void inc (int *n) { ++*n; }
...
int x = 5;
inc(&x);
cout << x << endl; // 6
```
`x`’s address is passed by value
`inc` changes the data at that address, which is visible to caller

### Question
Why `cin >> x` and not `cin >> &x`?
### Answer
C++ has another pointer-like type - **references**
- - - -
## References
```cpp
int y = 10;
int &z = y; // z is an lvalue reference to y
// like a const pointer - similar to
int *const z = &y;
```
**References** are like constant pointers with automatic dereferencing

`z = 12`  not `*z = 12`
now `y` is `12`

`int *p = &z` gives the address of `y`

In all cases, `z` behaves exactly like `y`
`z` is an **alias** for `y`

### Things you can’t do with lvalue references
* leave them uninitialized
	* e.g. `int &x;` or `int &x = 3;` or `int &x = y + z;`
	* must be initialized with something that has an address (an lvalue), since references are pointers
* create a pointer to a reference
	* e.g. `int &*x`
	* reference to a pointer is **allowed**
		* `int *&x = … ;`
	* create a reference to a reference is **not allowed**
		* `int &&x = … ;`
		* this means something different (later)
* create an array of references 
	* e.g. `int &r[3] = { …, …, … }`

### Things you can do
pass as function parameters
```cpp
void inc(int &n) { ++n; }
...
int x = 5;
inc(x);
cout << x << endl; // 6
```

why does `cin >> x` work? Takes `x` by reference
`istream &operator >>(stream &in, int &data)`
- - - -
## Pass by value
### Example
`int f(int n) { … }` copies the argument
	* if the argument is big, copy is expensive

### Example
```cpp
struct ReallyBig { … };

int f(ReallyBig rb) { ... } // copy = slow

int g(ReallyBig &rb) { ... } // alias = fast
// but it could change rb in the caller

int h(const ReallyBig &rb) { ... } // fast and immutable
```

### Advice
prefer pass-by-const-ref over pass-by-value for anything larger than a pointer, unless the function needs to create a copy anyway, then use pass-by-value

### Also
```cpp
int f(int &n) {...}
int g(const int &n) {...}

f(5); // can't initialize an lvalue ref n to a literal value
// if n changes, can't change the literal 5

g(5); // this is OK, since it's immutable, the compiler allows it
// how? creates a temporary location to hold the 5, so n has something to point at
```