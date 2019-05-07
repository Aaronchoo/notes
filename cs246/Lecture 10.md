# Lecture 10
#UW/f18/cs246

### Linked list node copy constructor
``` cpp
struct Node {
	...
	Node (const Node &other): data {other.data},
		next { other.next ? new Node { *other.next } : nullptr } {}
	// recursively copies the rest of the list
};
```

The copy constructor is called:
	1. when an object is initialized by another object of the same type
	2. when an object is passed by value
	3. when an object is returned by value from a function

**Note** Careful with constructors that can take one parameter:
- - - -
### Example
```cpp
struct Node {
	...
	Node (int data): data { data }, next { nullptr } {}
// single-arg constructors create implicit conversions

// eg
Node n {4};
// but also
Node n = 4 // implicit conversion from int to Node

int f (Node n);

f(4); // works - 4 implicitly converted to Node
};
```
Danger: accidentally passing an `int` to a function expecting a `Node`
	* silent conversion
	* compiler doesn’t signal an error
	* potential errors not caught

Disable the implicit conversion - make the constructor explicit

```cpp
struct Node {
	...
	explicit Node (int data): data { data }, next { nullptr } {}
};

Node n {4} // OK
Node n = 4; // Not OK
f(4); // Not OK
f(Node {4}); // OK
```
- - - -
## Destructors
When an object is destroyed
	1. destructor body runs (classes come with a destructor)
	2. fields’ destructors invoked in reverse declaration order (for fields that are objects)
	3. space deallocated

### When do we need to write a destructor?
```cpp
Node *np = new Node {1, new Node {2, new Node {3, nullptr}}};
```
If `np` goes out of scope
	* `np` is reclaimed (stack allocated)
	* all 3 nodes leaked (heap allocated)

If we say `delete np;`
	* calls `*np`’s destructor, which does nothing
- - - -
### Write a destructor to ensure the whole list is freed
```cpp
struct Node {
	...
	~Node() {
		// recursively calls *next's deconstructor
		delete next;
	}
};
```

Now `delete np;` frees the whole list
**delete nullptr is safe**
- - - -
### Copy Assignment Operator
```cpp
Student billy {70, 80, 90};
Student jane = billy; // copy constructor

Student joey; // default constructor
joey = billy; // copy, but not a construction
// copy assignment operator (uses built-in implementation)
```
May need to write your own:
```cpp
struct Node {
	...
	// return the thing assigned, so that cascading works
	Node &operator =(const Node &other) {
		data = other.data;
		delete next;
		next = other.next ? new Node { *other.next } : nullptr;
		return *this;
	}
};
```
**Dangerous**
Why?
```cpp
Node n {1, new Node {2, new Node {3, nullptr}}}
n = n;
*p = *q
a[i] = a[j]
```
* deletes n then tries to copy n to n
* undefined behaviour

When writing `operator=` always watch out for self-assignment
```cpp
struct Node {
	...
	Node &operator =(const Node &other) {
		if (this == other) return *this;
		data = other.data;
		delete next;
		next = other.next ? new Node { *other.next } : nullptr;

		return *this;
	}
};
```
if new fails, function quits immediately, assignment to next doesn't happen
next was deleted
dangling ptr
corrupted data structures

continued [Lecture 11 - Copy Assignment Operator](bear://x-callback-url/open-note?id=0288B93F-C8B2-41AE-934E-EF7CF36FD373-9111-000082AAA753060B&header=Copy%20Assignment%20Operator)