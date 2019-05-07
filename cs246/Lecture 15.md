# Lecture 15
#UW/f18/cs246
### Now consider the method isHeavy
when is the book heavy?
* for books > 200 pages
* for Texts > 500 pages
* for Comics > 30 pages
```cpp
class Book {
	...
public:
	bool isHeavy() const { return length > 200; }
};

class Text:public Book { // comic: similar
	...
public:
	bool isHeavy() const { return length > 500; }
};

Book b { ..., ..., 50 };
Comic c { ..., ..., 40, ... };

cout << b.isHeavy(); // false
cout << c.isHeavy(); // true
```
Since inheritance means “is a”, we can do this:
```cpp
Book b = Comic { ..., ..., 40, ... };
```
Question: is `b` heavy?
No, `b` is not heavy, `Book::isHeavy` runs.
Why?
`Comic` has more fields than `Book`, which has more memory.
so when assigning a `Comic` to a `Book`, the extra fields are lost, which makes it a `Book` object
- - - -
### Slicing
What happens when `Comic` is sliced?
The `hero` field is chopped off, `Comic` is coerced into a `Book`.
So `Book b = Comic { … }`, creates a `Book` and `Book::isHeavy` runs.
When accessing objects through pointers, slicing is unnecessary and does not happen
```cpp
Comic c = { ..., ..., 40, ... };
Book *pb = &c;
Comic *pc = &c;
pc->isHeavy(); // true
pb->isHeavy(); // false
```
and still, `Book::isHeavy` runs when we access `pb->isHeavy()`
Compiler uses the type of the pointer (or reference) to decide which `isHeavy` to run
	* doesn’t consider the actual type of the object
Same object behaves differently, depending on what type of pointer accesses it.
So a `Comic` is only a `Comic` when a `Comic` pointer (or ref) points to it?
Can we make `Comic` act like a `Comic`, even when pointed to buy the `Book` pointer?
- - - -
### Declare the method virtual
```cpp
class Book {
	...
protected:
	int length;
public:
	virtual book isHeavy() const { return length > 200; }
};

class Comic:public Book {
	...
public:
	bool isHeavy() const override { return length > 30; }
};

Comic c { ... ,... , 40, ... };
Comic *pc = &c;
Book *pb = &c;
Book &rb = c;
pc->isHeavy(); // true
pb->isHeavy(); // true
rb.isHeavy(); // true
```
`Comic::isHeavy` is used for each case
**virtual methods** - choose which class method to run based on the actual type of the object at runtime.
- - - -
### Example: my book collection
```cpp
Book *myBooks[20];
...
for (int i = 0; i < 20; ++i) {
	cout << myBooks[i]->isHeavy() << endl;
}
```
the `isHeavy` uses `Book::isHeavy` for books, `Comic::isHeavy` for comics and so on
- - - -
### Polymorphism (many forms)
**Accommodating multiple types under one abstraction**
Danger - consider
```cpp
class A {
	int x, y;
	...
};

class B:public A {
	int z;
	...
};

void f (A *a) {
	a[0] = A {6, 7};
	a[1] = A {8, 9};
}

B myArray[2] = {{1, 2, 3}, {4, 5, 6}};
f(myArray);
```
1,2,3 4,5,6 -> 6,7,8 9,5,6
Data is misaligned
**Never** use arrays of objects, polymorphically.
Use arrays of pointers.
- - - -
### Destructor with Inheritance
```cpp
class X {
	int *a;
	X(int n): a { new int[n]; } {}
	~X() { delete [] a }
};

class Y:public X {
	int *b;
public:
	Y(int n, int m): X {n}, b { new int [m]; }, {}
	~Y() { delete [] b; }
};

X *myX = new Y {10, 20}
delete myX;
```
Memory leaks occur
`delete myX` calls `~X`, but not `~Y`
so only `a`, but not `b`, was freed.
How can we ensure that deletion through superclass pointer will call the subclass destructor?
Make the destructor virtual
```cpp
class X {
...
public:
	virtual ~X() { delete [] a; }
};
```
**Always** make the destructor virtual in classes that are meant to have subclasses
	* even if the destructor doesn’t do anything