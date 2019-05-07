# Lecture 16
#UW/f18/cs246

# Lecture 16
## Pure Virtual Methods & Abstract Classes
```cpp
class Student {
	...
	public:
		virtual int fees() const;
};
```
We consider that there are 2 types of students:
	1. Regular 
	2. Co-op    -or fob and non-fob-
```cpp
class Regular:public Student{
	public:
		int fees() const override; // calculates regular student fees
};
class Coop:public Student{
	public:
		int fees() const override; // calculates coop student fees
};
```
問題: What should we put for `Student::fees()`? Every student is regular or co-op, so what should the superclass do then?
To solve this, we can explicitly give `Student::fees()` no implementation, since we do not have anything to give it. So we get:
```cpp
class Student{...
	public:
		virtual int fees() const = 0; 
		// this basically says I ain't got "no" implementation. We find out the truth later
};
```
This kind of method is called a **pure virtual method**. 
A class with pure virtual methods are called **abstract classes**. These classes cannot be instantiated. `Student s;` is now illegal. These abstract classes provide a way to organize subclasses together, for example in an array.
Subclasses of an abstract class are also abstract unless the pure virtual methods are implemented. Non-abstract classes are called **concrete**.
> In UMLs, write virtual and pure virtual methods in _italics_. Abstract classes are indicated by their name being in _italic_.  
- - - -
## Inheritance and Copy/Move
```cpp
class Book{
	// has the big 5 shits
};
class Text:public Book{
	// does not implement copy / move operations
};
Text t {😍😘,🤔👌,🤗🚨,🎃👻😎}; 
Text t2 = t; // there is only the default copy ctor in Text, what happens?
```
What the default copy ctor does with `Text`:
	* It calls the copy ctor of `Book`
	* It then copies field by field of `Text`
	* Same for other operations (move etc.)
So `Text t2 = t;` technically works.
Writing the copy operations for `Text`:
```cpp
// Copy ctor
Text::Text(const Text &other):Book{other},topic{other.topic} {}
// Copy assignment operator
Text &Text::operator=(const Text &other){
	Book::operator=(other);
	topic = other.topic;
	return *this;
}
```
Writing the move operations for `Text`:
```cpp
// Move ctor
// This following line is fucked, not correct
/* Although other "points at" an rvalue, other itself is an lvalue, so Book{other} will call the copy ctor instead */
Text::Text(Text &&other):Book{other},topic{other.topic} {}
// We use std::move() in <utility>. std::move(x) makes x get treated as an rvalue
// Now the move ctor of Book runs. other.topic is also an lvalue, so we also use move
Text::Text(Text &&other):Book{std::move(other)},topic{std::move(other.topic)} {}
// Move assignment
Text &Text::operator=(const Text &&other){
	Book::operator=(std::move(other));
	topic = std::move(other.topic);
	return *this;
}
```
The operations above are equivalent to the default, so specialize as needed like for `Node` or whatever.
- - - -
Now consider:
```cpp
Text t1{...}, t2{...};
Book *p1 = &t1, *p2 = &t2;
// What if we do 
*p1 = *p2;
// Here we get a partial assignment, only Book::operator= runs, and only Book fields are copied. So the Topic field in Text was not copied over from t2 to t1.
```
Since `Book::operator=` is not virtual, and we used a `Book *`, `Book::operator=` ran. We need to make it so that the `Text::operator=` runs.
* We can try making `Book::operator=` virtual.
```cpp
class Book{...
	public:
		virtual Book &operator=(const Book &other);
};
class Text:public Book{...
	public:
		Text &operator=(const Text &other) override;
};
```
Note: The two `operator=` are not the same method, so `override` triggers a compilation failure since the second method is not overriding anything.
We must change `Text` to:
```cpp
class Text:public Book{...
	public:
		Text &operator=(const Book &other) override;
};
```
This is due to the fact that `Book` and `Text` have a “is a” relationship. So `Text` has to be assignable from any `Book`. The parameter has to be of type `Book`.
But now, we do not know what specific kind of `Book` we copy. FUCK. Types will now mix and match.
```cpp
Text t{...};
Book b{...};
Comic c{...};
t = b; // would compile, but this is really BAD
t = c; // would compile, but REALLY BAD
```
This is called **mixed assignment**.
### We have a dilemma:
	* If `operator=` is non-virtual, partial assignment happens through base-class pointers
	* If `operator=` is virtual, we get mixed assignment
**Recommendation:** all superclasses should be _abstract_.
We’ve been using `Book` not as a superclass (a “general” book), but all books that are not `Texts` or `Comics` etc. 
So we need to rewrite our hierarchy, and this can solve our dilemma.
![](Lecture%2016/CF14625A-0A8B-45D5-9D73-05B218790DC2.png)
```cpp
class AbstractBook{
	string title, author;
	int length;
protected:
	// we only need the subclasses to call operator=
	AbstractBook &operator=(const AbstractBook &other);
public:
	AbstractBook(😍😘🤔👌🤗);
	...
	virtual ~AbstractBook() = 0; // this pure virtual dtor does not do shit except make your class abstract
};
// Now, this line from before will not compile anymore, since operator= is protected
*p1 = *p2; // this forces clients to be more precise with their assignments
```
So now, we have
```cpp
class NormalBook: public AbstractBook{
	public:
		NormalBook(😍😘🤔👌🤗🚨);
		~NormalBook();
		NormalBook &operator=(const NormalBook &other){
			AbstractBook::operator=(other);
			return *this;
		}
};
```
This prevents partial and mixed assignment. 
However, we have a problem. This does not link since `~AbstractBook()` does not have an implementation. When subclasses are destructed, the superclass dtor is also called, so an implementation is needed. 
```cpp
class NormalBook: public AbstractBook{
	// same shit as above, but add following
	AbstractBook::~AbstractBook() {}
```
- - - -
## Templates
🎉This is a 大 topic!!!!!!! すごいいいいい〜〜〜🎁
Remember:
```cpp
class List{
	struct Node{
		int data;
		Node *next;
	};
	Node *theList;
};
```
Now what if you wanna store `char`s HUH??? YOU FUCKED LOL.