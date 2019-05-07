# Lecture 12
#UW/f18/cs246

### I/O Operators
```cpp
struct Vec {
	...
	ostream &operator <<(ostream &out) {
		return out << x << ' ' << y;
	}
};
```
What’s wrong with this? Makes vec the first operand , not the second
	* use as `v << out;`, confusing

So define operator `<<` and `>>` as non-members
certain operators must be members:
	* operator `=`
	* operator `[]`
	* operator `->`
	* operator `()`
	* operator `T` (where `T` is a type)
- - - -
## Arrays of Objects
```cpp
struct Vec {
	int x, y;
	Vec (int x, int y): x {x}, y {y} {}
};

Vec *vp = new Vec[10]
Vec moreVecs[10]
```
Error
	* these want to call the default constructor on each item
	* If no default constructor, can’t initialize the items - error
- - - -
### Options
1. Provide a default constructor
2. Stack arrays:
```cpp
Vec moreVecs[3] = {{0,0}, {1,1}, {2,4}};
// Heap arrays of known size
Vec *np = new Vec[3] {{0,0}, {1,1}, {2,4}};
```
3. Heap arrays of dynamic size: create an array of pointers
```cpp
Vec **np = new Vec*[5]
vp[0] = new Vec {0,0};
vp[1] = new Vec {1,1};
// etc
for (int i = 0; i < 5; i++) delete vp[i];
delete [] vp;
```
- - - -
## Const Objects
```cpp
int f(const Node &n) { ... }
```
const objects arise often, especially as params
What is a const object? - can’t change its fields.
Can we call methods on const obj?
Issue: the method might modify fields, violate `const`
We can call methods that promise not to modify fields
- - - -
### Example
```cpp
struct Student {
	int assns, mt, final;
	float grade() const; // doesn't modify fields
};
```
Compiler checks that const methods don’t modify fields
Only const methods can be called on const objects
- - - -
### Now Consider: want to collect usage stats on Student objects
```cpp
struct Student {
	...
	int numCalls = 0;
	float grade() { // now can't call grade on const students
		++numCalls;
		return ____;
	}
};
```
But mutating `numCalls` affects only the **physical constness** of `Student` objects, not the **logical constness**

want to be able to update `numCalls`, even if the object is `const` - declare the field mutable
```cpp
struct Student {
	...
	mutable int numCalls = 0;
	float grade() const {
		++numCalls;
		return _____;
	};
};
```
- - - -
## Static Fields +  Methods
### Static Fields
	* `numCalls` tracked # times a method was called on a particular object.
	* What  if we want to track # times a method is called over all Students?
	* Or track how many Students are created?

**static members**: associated with the class itself, not with any particular object.
```cpp
struct Student {
	...
	static int numStudents;
	Student(...): ... {
		++numStudents;
	}
};
```
static fields must be defined external to the class
```cpp
int Student::numStudents = 0; // in .cc file
```
- - - -
### Static Member Functions
* don’t depend on a specific instance (no this param)
* can only access static fields and call other static member functions
```cpp
struct Student {
	...
	static int numStudents;
	...
	static void howMany() {
		cout << numStudents;
	}
};

// in .cc
Student billy {60,70,80}, jane {70,80,90};
Student::howMany(); // 2
```
- - - -
## Invariants and Encapsulation
Consider
```cpp
struct Node {
	int data;
	Node *next;
	~Node() { delete next; }
};

Node n1 {1, newNode {2, nullptr}};
Node n2 {3, nullptr};
Node n3 {4, &n2};
```
What happens when these go out of scope?
	* n1: destructor runs, entire list is deleted. OK.
	* n3: destructor tries to delete &n2, but n2 is on the stack, not the heap! Undefined behaviour

`Node` relies on an assumption for its proper operation - that next is either `nullptr`, or was allocated by new
This is an **invariant** - statement that must hold true that `Node` relies on
But we can’t guarantee this invariant - can’t trust the user to use `Node` properly
- - - -
### Example - Stack
* invariant: last item pushed is the first item popped
* but not if the client can rearrange the underlying data
* Hard to assume about programs if you can’t rely on invariants
- - - -
### Encapsulation
* treat objects as black boxes (capsules)
	* seal away implementation
	* only interact via private methods
* abstraction
* regain control
- - - -
### Example
```cpp
struct Vec {
	// also public by default
	Vec (int x, int y);

	private:
		// can't be accessed outside struct Vec
		int x, y;
	public:
		// anyone can see
		Vec operator +(const Vec &other);
	...
};
```
In general: **want private fields; only methods should be public**
Better to have default visibility = private
- - - -
### Switch from Struct to Class
```cpp
class Vec {
	int x, y;

	public:
		Vec(int x, int y);
		Vec operator +(const Vec &other);
};
```
Difference is **default visibility**
	* public in `struct`
	* private in `class`
- - - -
### Fix Linked Lists
```cpp
// list.h
class List {
	struct Node; // private nested class - only accessible inside class List
	Node *theList = nullptr;

	public:
}
```
[Lecture 13 - Recall: Fixing the linked list class](bear://x-callback-url/open-note?id=558EBF7C-731D-4AB6-8E00-138585812198-14654-0000A29A2AA5C3E7&header=Recall:%20Fixing%20the%20linked%20list%20class)