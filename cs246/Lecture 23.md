# Lecture 23
#UW/f18/cs246
## Casting
1. static_cast
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
2. reinterpret_cast
	* unsafe, implementation-specific, “weird conversions”
```cpp
Student s;
Turtle *t = reinterpret_cast<Turtle*>(&s);
```
3. const_cast
	* for converting between const and non-const
	* the only c++ cast that can “cast away const”
```cpp
void g(int *p);

void f(const int *p) {
	...
	g(const_cast<int *>(p));
	...
}
```
4. dynamic_cast
	* is it safe to convert a `Book*` to a `Text*`
```cpp
Book *pb = ...;
static_cast<Text*>(pb)->getTopic(); // safe?
```
	* depends on what `pb` actually points at
	* better to do a tentative cast, try it and see if it succeeds
```cpp
Book *pb = ...;
Text *pt = dynamic_cast<Text*>(pb);
```
	* if the cast works (`*pb` really is a `Text`, or a subclass of `Text`), `pt` points at the obj
	* if the cast fails, then `pt` will be `nullptr`
```cpp
if (pt) cout << pt->getTopic();
else cout << "Not a Text";
```
	* can we do this with smart pointers? `static_pointer_cast`, `dynamic_pointer_cast`
	* cast shared_pointers to shared_pointers
	* can use dynamic casting to make decisions based on an object’s Run-Time Type Information (RTTI)
```cpp
void whatIsIt(shared_ptr<Book> b) {
	if (dynamic_pointer_cast<Comic>(b)) cout << "Comic";
	else if (dynamic_pointer_cast<Text>(b)) cout << "Text";
	else cout << "Book";
}
```
	* code like this is tightly coupled to the `Book` class hierarchy and may indicate bad design
	* better: use virtual methods or write a visitor (if possible)
	* dynamic casting also works with references
```cpp
Text t{...};
Book &b = t;
Text &t2 = dynamic_cast<Text&>(b);
```
	* if b “points to” a Text, then t2 is a ref to the same Text
	* if not … (no such thing as a null reference), raises exception std::bad_cast
	* note: dynamic casting only works on classes with at least one virtual method
	* with dynamic ref casting, we can “solve” the polymorphic assignment problem
```cpp
Text &Text::operator=(const Book &other) { //virtual
	// throws if other is not a Text
	const Text &textOther = dynamic_cast<const Text&>(other);
	if (this == &textOther) return *this;
	Book::operator=(other);
	other = textOther.topic;
	return *this;
}
```
- - - -
## How virtual methods work
```cpp
class Vec {
		int x, y;
	public:
		int f();
};

class Vec2 {
		int x, y;
	public:
		virtual int f();
};
```
what is the difference?
```cpp
Vec v {1,2};
Vec2 w {1,2};

cout << sizeof(v) << ' ' << sizeof(w) << endl; // 8 16
```
first note: 8 is space for 2 ints, no space for the `f` method
compiler turns methods into ordinary functions and shares them separately from objects
recall:
```cpp
Book *pb = new Book; // or Text, Comic
auto pb = make_unique<Book>(); // or Text, Comic
pb->isHeavy();
```
if `isHeavy` is virtual, choice of which version to run is based on the type of the actual object
which the compiler can’t know in advance
correct `isHeavy` must be chosen at runtime, how? (implementation-specific, but every compiler does it this way)
for each class with virtual methods, the compiler creates a table of function pointers (the vtable):
```cpp
class C {
	int x, y;
	virtual void f();
	virtual void g();
	void h();
	virtual ~C();
};
```
C objects have an extra pointer (the vptr) that points to C’s vtable 

![](Lecture%2023/Screen%20Shot%202018-11-27%20at%2012.43.51%20PM.png)
### calling a virtual method (at run time):
	* follow vptr to vtable
	* fetch pointer to actual method from table
	* follow the function pointer and call the function
	* thus virtual function calls incur a small cost
	* also declaring at least one virtual function adds a ptr to the object, also a space cost
concretely, how is an object laid out? compiler-dependent
### g++
```cpp
class A {
	int a, c;
	virtual void f();
};

class B:public A {
	int b, d;
};
```

![](Lecture%2023/Screen%20Shot%202018-11-27%20at%2012.48.45%20PM.png)
![](Lecture%2023/Screen%20Shot%202018-11-27%20at%2012.49.40%20PM.png)
so we always know where the vptr is
so a pointer to B looks like a ptr to A if you ignore the last 2 fields