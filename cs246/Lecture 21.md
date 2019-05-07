# Lecture 21
#UW/f18/cs246
### Recall: Coupling
**cohesion** - how closely elements of a module are related to each other
**low cohesion** 
	* arbitrary grouping of unrelated elements (e.g. utility library)
	* elements share a common theme, otherwise unrelated (e.g. <algorithm>
	* elements manipulate state over the lifetime of an object (e.g. open.read/close files)
	* elements pass data to each other
**high cohesion**
	* elements cooperate to perform exactly one task
**Goal:** low coupling, high cohesion
- - - -
## Decoupling the Interface (MVC)
Your primary program classes should not print things
```cpp
class ChessBoard {
	...
	cout << "Your move" << endl;
};
```
Bad design - inhibits code reuse
what if you want to reuse ChessBoard, but not have it communicate via `stdout`?
one solution: give the class stream objects for I/O:
```cpp
class ChessBoard {
	istream &in;
	ostream &out;
public:
	ChessBoard(istream &in, ostream &out): int {in}, out {out} {}
	...
	out << "Your move" << endl;
};
```
Better - but what if we don’t want to use streams at all?
ChessBoard should not be doing any communication at all
- - - -
### Single Design Principle
> A class should only have one reason to change  
* game play and communication are two reasons
* communicate with the ChessBoard via params / results / exceptions
* confine user communication outside the game class
can’t communicate in main - hard to reuse code
have a class for interaction, that is separate from the game play classes
- - - -
## Architecture: Model-View-Controller (MVC)
separate the distinct notions of the data (“model”)
the presentation of the data (“view”)
and the manipulation of the data (“controller”)
**Model**
* can have multiple views (e.g. text and graphics)
* doesn’t need to know about their details
* class Observer Pattern (or could communicate through the controller)
**Controller**
* mediate control flow between model and view
* e.g. turn-taking or full game rules
* may communicate with the user for input (or this could be the view)

by decoupling presentation and control, MVC promotes reuse
- - - -
### Exception Safety
consider
```cpp
void f() {
	MyClass mc;
	MyClass *p = new MyClass;
	g();
	delete p;
}
```
no leaks - what happens if `g` throws?
* during stack-unwinding, all stack allocated data is cleaned up
* destructors run, memory reclaimed
* heap-allocated memory is not destroyed
* if g throws, `*p` is leaked (`mc` is not)
```cpp
void f() {
	MyClass mc;
	MyClass *p = new MyClass;
	try {
		g();
	}
	catch(...) {
		delete p;
		throw;
	}
	delete p;
}
```
ugly and error-prone - duplication of code
how else can we guarantee that something (i.e. delete p) will happen no matter howe we exist f? (normal or error)
in some languages - “finally” clauses that guarantee certain final actions - not in C++
only thing you can count on in C++ - destructors for stack-allocated data will run
therefore use stack-allocated data with destructor as much as possible
use the guarantee to your advantage
- - - -
### C++ idiom: RAII - Resource Acquisition Is Initialization
every resource should be wrapped in a stack-allocated object, whose destructor deletes it
e.g. files
```cpp
{
	// acquiring the resouce ("file") = initializing the object (f)
	ifstream f {"file"};
}
```
the file is guaranteed to be released when f is popped from the stack (`f`’s destructor runs)
this can be done with dynamic memory
```cpp
class std::unique-ptr<T> // (#include <memory>)
```
* takes a `T*` in the constructor
* the destructor will delete the pointer
* in between - can dereference, just like a pointer
```cpp
void f() {
	MyClass mc;
	std::unique_ptr<MyClass> p { new MyClass };
	g();
}

// or 

void f() {
	MyClass mc;
	auto p = std::make_unique<MyClass>(); // pass in constructor arguments into make_unique call
	// p is a unique_pointer to MyClass
	g();
}
```
no leaks. guaranteed.
- - - -
### Difficulty - two pointers pointing at the same object
```cpp
class C {...};

unique_ptr<C> p { new C {...} };
unique_ptr<C> q = p;
```
what happens when a `unique_ptr` is copied? - don’t want to delete the same pointer twice
instead - copying is disabled for `unique_ptr`, they can only be moved
sample implementation:
```cpp
template <typename T> class unique_ptr {
	T *ptr;
public:
	explicit unique_ptr(T *p): ptr {p} {}
	~unique_ptr() { delete ptr; }
	unique_ptr(const unique_ptr<T> &other) = delete;
	unique_ptr &operator=(const unique_ptr<T> &other) = delete;
	unique_ptr(unique_ptr<T> &&other): ptr {other.ptr} { other.ptr = nullptr; }
	unique_ptr<T> &operator=(unique_ptr<T> &&other) {
		std::swap(ptr, other.ptr);
		return *this;
	}
	T &operator*() { return *ptr }
};
```
if you need to be able to copy pointers - first answer the question of ownership
* who will own the resource?
* who will have responsibility for freeing it?
* that pointer should be a `unique_ptr`
* all other pointers can be raw pointers
* can fetch the underlying raw pointer with `p.get()`
if there is true shared ownership, i.e. any of several pointers might free the resource - use `std::shared_ptr`