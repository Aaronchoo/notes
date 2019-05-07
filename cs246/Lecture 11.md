# Lecture 11
#UW/f18/cs246

### Copy Assignment Operator
Attempts 1 and 2: [Lecture 10 - Copy Assignment Operator](bear://x-callback-url/open-note?id=D7F23485-7229-45BE-9B15-B7376C4DAA67-14143-000116FF4B4EEEA3&header=Copy%20Assignment%20Operator)

would be better to create the copy first, then delete.
```cpp
Node& operator=(const Node& o) {
	if (this == &o) return *this;
	Node *tmp = next;
	next = o.next ? new Node { *o.next } : nullptr;
	data = o.data
	delete tmp;
	return *this; // if new fails, Node has old data
}
```

We can employ the copy-and-swap idiom to achieve the advantages of this version more concisely
```cpp
#include <utility>

struct Node {
	void swap(Node &o) {
		using std::swap; // i mean std::swap
		swap(data, o.data); // i say swap
		swap(next, o.next);
	}
}
```

Attempt 4
```cpp
Node& =operator(const Node &o) {
	Node tmp = o; // copy constructor
	swap(tmp);
	return *this; // tmp is deleted by going out of scope
}
```
- - - -
## Rvalues and rvalue references
### Recall
* an lvalue is anything with an address
* an lvalue reference (&) is like a const pointer with auto-deref, always initialized to an lvalue

### Consider
```cpp
Node n { 1, new Node { 2, nullptr } }
Node m = n; // copy constructor
Node m2;
m2 = n; // copy assign operator
Node plusOne(Node n) {
	for (Node *p = &n; p; p = p->next) {
		++p.data;
	}
	return n;
}
Node m3 = plusOne(n); // other
```
What function is called? copy constructor?
If the definition of `m3 `invokes copy constructor (it does), what is other?
Other is an lvalue ref, but what is the address of the object it points at?
The complier creates a temporary object to hold the result of `plusOne(n)`
Other is a ref to this temporary, and the copy constructor  deep-copies from the temp

But 
	* temp is going to be discarded anyways, as soon as `Node m3 = plusOne(n)` ends
	* it is wasteful to have to copy from the temp
	* so let’s steal from it instead
	* we need to know if parameter is a temp or a standalone object

In C++, an **rvalue reference** of type `Node &&` is a reference to a temporary object (an r value) of type Node

### We can write a constructor that takes a Node &&
```cpp
struct Node {
	...
	Node (Node &&o): data {o.data}, next {o.next} {
		o.next = nullptr;
		// is so the data we stole is not deleted when o is deleted
	}
}
```
What should this do? Steal other’s data!

### Similarly
```cpp
Node m;
m = plusOne(n); // assignment from a temp
```
Again, to avoid copying from the temp whose data is about to be deleted, write a move assignment operator

### Move assignment operator
```cpp
Node &operator=(Node &&o) { // steal other's data, and other will destroy mine
	using std::swap;
	swap(data, o.data);
	swap(next, o.next);
	return *this; // o goes out of scope and deletes our data
}
```
If you don’t define a move constructor/assignment operator, the copy versions are used.
If the move constructor/move assignment operator is defined, it will replace all calls to the copy constructor / copy assignment operator where the arg is a temp (rvalue). The compiler takes care of this.

### Copy/Move Elision
```cpp
Vec makeVec { return { 0, 0 }; }
Vec v = makeVec(); // what runs here?
```
could be copy constructor or a move constructor
Not sure, in g++, just a basic constructor would be called - no copy or move
In some circumstances, C++ is allowed to (but doesn’t have to) skip calling copy/move constructors
In the example, 	`makeVec` writes the result directly into the space occupied by `v` in the caller, rather than creating the object in its own stack frame and then copying it.

### Another Example
```cpp
void doSomething(Vec v) { //pass by value calls move/copy constructor
	...
}

doSomething(makeVec()); // normally calls a move constructor to construct v in doSomething
```
Under copy elision, the result of `makeVec()` would be written directly into the param of `doSomething`, saving a move
Copy elision is allowed, even if dropping the constructor call would change the behaviour  of the program
e.g. the constructor prints something
You are not expected to know when copy elision happens
You are expected to know its possible
If you really want all omitted constructors to be run, in g++ you can pass the option `-f no-elide-constructors` But this is your program considerably

### In summary, we have the rule of 5
If you need a custom version of any of:
	1. Copy constructor
	2. Copy assignment operator
	3. Destructor
	4. Move constructor
	5. Move assignment operator
You usually need a custom version of all five.

## Member Operators
In the previous discussion, we had operator `=` as a member function and not a standalone function
When an operator is a member function, this plays the role of the LHS arg:
```cpp
struct Vec {
	...
	Vec operator+(const Vec &rhs) {
		return { x + rhs.x, y + rhs.y };
	}

	Vec operator*(const int k) {
		return { x * k, y * k };
	}
};
```

What if i want `k * v`?
It cannot be a member function, because the first arg is an `int `not a `Vec`. So it must bbe a standalone:
```cpp
Vec operator*(const int k, const Vec &v) {
	return v*k;
}
```