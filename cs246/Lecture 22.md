# Lecture 22
#UW/f18/cs246
### Recall std::shared_ptr
```cpp
{
	auto p1 = std::make_shared<MyClass>();
	if (...) {
		auto p2 = p1;
	} // p2 popped, ptr not deleted
} // p1 popped, ptr is deleted
```
shared_ptrs maintain a **reference count** - count of all shared_ptrs pointing at that object
memory is freed when the ref count reaches 0
consider:
```scheme
(define l1 (cons 1 (cons 2 (cons 3 empty))))
(define l2 (cons 4 (rest l1)))
```
use the type of ptr that accurately reflects the pointer’s ownership role
dramatically fewer opportunities for leaks
- - - -
### Back to exception safety
3 levels of exception safety guarantees
1. basic guarantee
	* if an exception occurs, the program will be in some valid state
	* nothing is leaked, class invariants maintained, no corrupted data structures
2. strong guarantee
	* if an exception raised while executing f, the state of the program will be as if f had not been called
3. no throw guarantee
	* f will never throw an exception, and will always achieve its purpose
- - - -
### Example
```cpp
class A {...}; class B {...};
class C {
		A a;
		B b;
	public:
		void f() {
			// may throw (strong guarantee)
			a.g();
			b.h();
		}
};
```
Is `C::f` safe?
* if `a.g()` throws nothing has happened yet. OK
* if `b.h()` throws, effects of g would have to be undone to offer strong guarantee
	* very hard or impossible if g has non-local side-effects
thus no, probably not exception safe
if g + h do not have non-local side-effects, can use copy + swap
```cpp
class C {
	...
	void f() {
		A aTemp = a;
		B bTemp = b;
		// if these throw, the original a + b are still intact
		aTemp.g();
		bTemp.h();
		// but what if copy assignment throws?
		a = aTemp;
		b = bTemp;
		// better if swap was no throw - assigning pointers cannot throw
	}
};
```
- - - -
### solution: pImpl idiom
```cpp
struct CImpl {
	A a;
	B b;
};

class C {
	unique_ptr<CImpl> pImpl;
	...
	// strong guarantee
	void f() {
		auto tmp = make_unique<CImpl>(*pImpl);
		tmp->a.g();
		tmp->b.h();
		std::swap(pImpl, tmp); // no throw
	}
};
```
- - - -
## Exception Safety + the STL:vectors
vectors
	* encapsulate a heap-allocated array
	* follow RAII - when a stack-allocated vector goes out of scope, the internal heap-allocated array is freed
```cpp
void f() {
	vector<MyClass> v;
	...
} // v goes out of scope - array is freed, MyClass destructor runs on all objects in the vector
```
but
```cpp
void g() {
	vector<MyClass*> v;
	...
}
// arrays freed - pointers don't have destructors, so any objects pointed to by pointers are not deleted
// v doesn’t know whether the pointers in the array own the objects they point at
for (auto &x:v) delete x;
```
but
```cpp
void h() {
	vector <unique_ptr<MyClass>> v;
	...
}
// array is freed, unique_ptr destructor runs, so the objects are deleted
// no explicit deallocation
```
- - - -
`vector<T>::emplace_back` 
* offers the strong guarantee
	* if the array is full (size == cap)
		* allocate new array
		* copy objects over (copy constructor)
		* delete old array
	* if a copy constructor throws
		* destroy the new array
		* old array still intact
		* strong guarantee
but copying is expensive and old array will be throw away
wouldn’t moving the objects be more efficient?
	* allocate new array
	* move objects over (move constructor)
	* delete old array
if move constructor throws
	* can’t offer the strong guarantee
	* original no longer intact
if the move constructor offers no throw guarantee, emplace_back will use the move constructor
else it uses the copy constructor, which may be slower
so your move operators should provide no throw guarantee, and you should indicate that they do:
```cpp
class MyClass {
	public:
		MyClass(MyClass &&other) noexcept {...}
		MyClass &operator=(MyClass &&other) noexcept {...}
};
```
if you know that a function will never throw or propagate an exception, declare it noexcept
facilitates optimization
at minimum: moves + swaps should be noexept
- - - -
## Casting
In C:
```c
Node n;
int *cp = (int *) &n; // cast - forces c++ to treat a Node* as an int*
```
C-style casts should be avoided in c++. If you must cast, use a c++ style cast
**4 kinds**
1. static-cast
	* ‘sensible’ casts with well-defined meanings
	e.g. double -> int
```cpp
double d;
void f(int x);
void f(double x);
f(static_cast<int>(d));
```
	* superclass ptr -> subclass ptr
```cpp
Book *b = new Text {...};
Text *t = static_cast<Text*>(b);
```
you are taking responsibility that b actually points at a Text “trust me”
2. reinterpret-cast
	* unsafe, implementation-specific, “weird conversions”
```cpp
Student s;
Turtle *t = reinterpret_cast<Turtle*>(&s);
```