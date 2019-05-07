# Lecture 13
#UW/f18/cs246

### Recall: Fixing the linked list class
```cpp
// list.h
class List {
	// private nested class - only accessible within class List
	struct Node;
	Node *theList = nullptr;

public:
	void addToFront(int n);
	int & ith(int i ); // can mutate item
	~List();
};
```

```cpp
// list.cc
#include <list.h>

struct List::Node {
	int data;
	Node *next;
	Node( ... ): ... {...}
	~Node() { delete next }
}

void List::addToFront(int n) {
	theList = new Node { n, theList };
}

int & List::ith(int i ) {
	Node *curr = theList;
	for (int n = 0; n < i; curr = curr->next);
	return curr->data;
}
```

Only `List` can create/manipulate node objects
Therefore can guarantee the invariant that `next` is always nullptr or allocated buy new.
[Lecture 12 - Invariants and Encapsulation](bear://x-callback-url/open-note?id=4CB22FBD-7168-450C-8B4C-2FB662241483-19124-0000BE41D5CB12C1&header=Invariants%20and%20Encapsulation)

* But now we can’t traverse the list from node to node as we would a linked list
* Repeatedly calling `ith` to access the whole list is `O(n^2)` time.
* But we can’t expose the nodes or we lost encapsulation
- - - -
## SE Topic: Design Patterns
* certain programming problems arise often
* keep track of good solutions to these problems
* reuse and adopt them

### Solution - Iterator Pattern
* create a class that manages access to nodes.
	* abstraction of a pointer
	* walk the list without exposing the actual pointers

```c
for (int *p = arr; p != arr + size; ++p)
	printf("%d\n", *p);
```
We want `Iterator` to be used like the pointer `p` above
```cpp
class List {
	struct Node;
	Node * theList = nullptr;

public:
	class Iterator {
		Node *p;
	public:
		explicit Iterator(Node *p): p {p} {}
		int & operator*() {
			return p->data;
		}
		Iterator & operator++() {
			p = p->next;
			return *this;
		}
		bool operator!=(const Iterator & other) {
			return p != other.p
		}
	}; // end of class iterator
	Iterator begin() { // still within class List
		return Iterator { theList };
	}
	Iterator end() {
		return Iterator { nullptr };
	}
	... // other List methods
};
```
Client:
```cpp
int main() {
	int l;
	l.addToFront(1);
	l.addToFront(2);
	l.addToFront(3);
	// O(n) traversal
	for (List::Iterator it = l.begin(); it != l.end(); ++it) {
		cout << *it << endl;
	}
}
```
- - - -
### Automatic type deduction
```cpp
auto x = y;
```
declares `x` to have the same type as its initializer `y`

```cpp
for (auto it = l.begin(); it != l.end(); ++it) {
	cout << *it << endl;
}
```
- - - -
### Ranged-based for loop
```cpp
for (auto n:l) {
	cout << n << endl;
}
```
* Available for any class with methods `begin` and `end` that produce iterators
* the iterator must support `!=`, prefix `++`, unary `*`

If you want to mutate list items (or save copying):
```cpp
for (auto &n:l) [
	++n;
}
```
Will visit iterators later in the course
- - - -
### Encapsulation continued
List client can create iterators directly:
```cpp
auto it = List::Iterator { nullptr };
```
violates encapsulation - client should be using begin/end
We could
	* make Iterator’s constructor private
	* then client can’t call `List::Iterator { … };`, but neither can `List`
### Solution
* give list privileged access to Iterator
* make it a `friend`
```cpp
class List {
	...
public:
	class Iterator {
		Node *p
		explicit Iterator(Node *p);
	public:
		...
		friend class List;
	};
};
```
Now `List` can still create iterators, but client can only create iterators by calling begin/end
**Give your classes as few friends as possible - weakens encapsulation**
Provide access to private fields - accessor/mutator methods
```cpp
class Vec {
	int x, y;
public:
	...
	int getX() const { return x; } // accessor
	void setY(int z) { y = z; } // mutator
```
What about `operator<<` 
	* needs x, y but can’t be a member
	* if `getX`, `getY` defined then ok
	* but if you don’t want to provide getX, getY
		* make `operator<<` a friend function

```cpp
// vec.h
class Vec {
	...
	friend std::ostream & operator<<(std::ostream & out, const Vec &v);
```

```cpp
// vec.cc

ostream & operator<<(ostream &out, const Vec &v) {
	return out << v.x << " " << v.y;
}
```
- - - -
## SE Topic: System Modelling
Visualize the structure of the system (abstraction + relationships among them) to aid design and implementation
### Popular Standard: UML (Universal Modelling Language)
![](Lecture%2013/Screen%20Shot%202018-10-25%20at%2011.32.06%20AM.png)