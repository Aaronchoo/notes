# Lecture 17
#UW/f18/cs246

## Templates
```cpp
class List {
	struct Node {
		int data;
		Node *next;
	};
	Node *theList;
};
```
What if you want to store something else? Whole new class?
**template** - class parameterized by a type

```cpp
template <typename T> class Stack {
	int size, cap;
	T *contents;
public:
	Stack() { ... }
	void push(T x) { ... }
	T &top() { ... }
	void pop() { ... }
};

template <typename T> class List {
	struct Node {
		T data;
		Node *next;
	};
	Node *theList;
public:
	class Iterator {
		...
	public:
		T &operator*() { ... }
		...
	};
	T &ith(int i) { ... }
	void addToFront(T n);
	...
};
```

Client:
```cpp
List<int> l1;
List<List<int>> l2;
l1.addToFront(3);
l2.addToFront(l1);

for (List<int>::Iterator it = l1.begin(); it != l1.end(); ++it) {
	cout << *it << endl;
}
// or
for (auto n:l1) cout << n << endl;
```

Compiler specializes templates at the source code level, before compilation.
- - - -
## The Standard Template Library (STL)
Large number of useful templates.

### Example: dynamic-length arrays (vectors)
```cpp
#include <vector>
std::vector<int> v {4,5}; // 4, 5
v.emplace_back(6); // 4, 5, 6
v.emplace_back(7); // 4, 5, 6, 7
```
### Note
```cpp
vector<int> v (4,5); // 5 5 5 5
```
### Looping over vectors
```cpp
for (int i = 0; i < v.size(); i++) {
	cout << v[i] << endl;
}

for (vector<int>::iterator it = v.begin(); it != v.end(); ++it) {
	cout << *it << endl;
}

for (auto n:v)
	cout << n << endl;

// to iterate in reverse

for (vector<int>::reverse_iterator it = v.rbegin(); it != v.rend(); ++it) {
	...
}
// can't use ranged loop for reverse

v.pop_back() // remove last element

// use iterators to remove items frmo inside a vector
auto it = v.erase(v.begin()); // erases element 0
it = v.erase(v.begin() + 3); // erases element 3 (4th)
// returns an iterator to the first item after the erase
it = v.erase(it); // so we can erase the next item
it = v.erase(v.end() - 1); // erases last item
```
- - - -
### `v[i]`
* i-th element of v
* unchecked - if you go out of bounds, undefined behaviour
- - - -
### `v.at(i)`
* checked version of `v[i]`
* what happens when you go out of bounds?
### Problem
* vector’s code can detect the error, but doesn’t know what to do about it
* client can respond, but can’t detect the error
### C solution
* function return a status code, or set the global variable `errno`
* leads to awkward programming
* encourages programmers to ignore error-checks
### C++
* when an error condition arises, the function **raises an exception**
* what happens? by default, execution stops
* but we can write **handlers**to **catch** exceptions and deal with them
- - - -
### Exceptions and Handlers
`vector<T>::at` raises the exception `std::out_of_range` when it fails
we can handle it as follows
```cpp
#include <stdexcept>
...
try {
	cout << v.at(10000); // statement that may raise go in a try block
	... // things to do on success
}
// out_of_range is the class and r is the object
catch (out_of_range r) {
	cerr << "Range error" << r.what() << endl;
}
```
- - - -
### Now consider
```cpp
void f() {
	throw out_of_range { "I AM ERROR" };
	// ctor call
}
void g() { f(); }
void h() { g(); }

int main() {
	try {
		h();
	}
	catch (out_of_range) { ... }
}
```
what happens?
1. main calls h
2. h calls g
3. g calls f
4. f throws
control goes back through the call chain (unwinds the stack)
until a handler is found, all the way back to main, main handles the exception
no matching handler found -> program terminates

A handler can do part of the recovery job, i.e. execute some corrective code and throw another exception:
```cpp
try { ... }
catch (SomeErrorType s) {
	...
	throw SomeOtherError { ... };
}
// or rethrow the same expression
try { ... }
catch (SomeErrorType s) {
	...
	throw;
}
```
`throw` vs `throw s`
`SpecialErrorType` is derived from `SomeErrorType`
`s` might actually be either of these
`throw` - actual type of s is retained
`throw s` - throws a new exception of type `SomeErrorType` (sliced copy of s)