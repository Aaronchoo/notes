# Lecture 20
#UW/f18/cs246

### Example
add a visitor to the Book hierarchy
```cpp
class Book {
	public:
		...
		virtual void accept(BookVisitor &v) { v.visit(*this); }
};

class Text:public Book {
	public:
		...
		void accept (BookVisitor &v) { v.visit(*this); }
};

class BookVisitor {
	public:
		virtual void visit(Book &b) = 0;
		virtual void visit(Text &t) = 0;
		virtual void visit(Comic &c) = 0;
};
```
### Application
track how many of each type of book I have
Books - by author
Texts - by topic
Comics - by hero
use a `map<string, int>`
could add `void updateMap(…)` to each class
or write a visitor
```cpp
struct Catalogue:public BookVisitor {
	map<string,int> theCat;
	void visit(Book &b) override { ++theCat[b.getAuthor()]; }
	void visit(Text &t) override { ++theCat[t.getTopic()]; }
	void visit(Comic &c) override { ++theCat[b.getHero()]; }
};
```
but it won’t compile, why?
`book.h` and `BookVisitor.h` include each other
	* circular include dependency
	* `book.h` won’t be included a second time - include guard
	* Result: `Text` doesn’t know what `Book` is
	* are these includes really needed?
- - - -
## Compilation Dependencies
when does a compilation dependency need to exist?
consider
```cpp
class A {...} // A.h

#include "A.h"
class B:public A {
	...
};

#include "A.h"
class C {
	A a;
};

class A; // there is a class A (foward declared)
class D {
	A *a;
};

class A;
class E {
	A f(A x);
};

#include "A.h"
class F {
	A f(A x) { ...x.someMethod()... }
};
```
if the code doesn’t need an include, don’t create a needless compilation dependency by including unnecessarily
when class A changes, only A,B,C,F need recompilation
### In the implementations of D,C:
```cpp
// d.cc
void D::f() {
	// need to include class A
	// a true compilation dependency
	a->someMethod();
};
```
- - - -
## XWindow Class
```cpp
class XWindow {
		// this is private data, yet we can look at it
		// do we know what it all means? do we care?
		Display *d;
		Window w;
		int s;
		GC gc;
		unsignedlong colours[10];
	public:
};
```
what if I add or change a private member?
all clients must recompile
would be better to hide these details away
### Solution: pimpl idiom (“pointer to implementation”)
create a second class `XWindowImpl`
```cpp
// XWindowImpl.h
#include <X11/Xlib.h>

struct XWindowImpl {
	Display *d;
	Window w;
	int s;
	GC gc;
	unsigned long colours[10];
};
```
```cpp
// window.h

class XWindowImpl;
class XWindow {
		XWindowImpl *pImpl;
	public:
		... // no change
}
```
* no need to include `Xlib.h`
* forward declare the impl class
* **no** compilation dependency on `XWindowImpl.h`
* clients also don’t depend on `XWindowImpl.h`
```cpp
// Window.cc
#include "Window.h"
#include "XWindowImpl.h"

XWindow::XWindow(...): pImpl { new XWindowImpl } {...}
// other methods - replace fields d, w, s, etc. with pImpl->d, pImpl->w, pImpl->s, etc.
```
- - - -
### Generalization
what if there are several possible window implementation, say `XWindows` + `YWindows`
then we make the `Impl`  struct a superclass
![](Lecture%2020/Screen%20Shot%202018-11-15%20at%2012.44.15%20PM.png)
pImpl idiom with a class hierarchy of implementations - called the `Bridge Pattern`
- - - -
## Measures of Design Quality
coupling and cohesion
**coupling**: how much distinct program modules depend on each other
* low coupling
	* modules communicate via function calls with basic params/results
	* modules pass arrays/structs back and forth
	* modules affect each other’s control flow
* high coupling
	* modules have access to each other’s implementation (friends) 