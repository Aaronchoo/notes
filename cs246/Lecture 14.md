# Lecture 14
#UW/f18/cs246

## Relationship: composition of classes
```cpp
class Vec {
	int x, y;
	Vec (int x, int y):
		x { x }, y { y } {}
};
// two vecs define a basis
class Basis {
	Vec v1, v2;
	...
};

Basis b;
```
Doesn’t compile
	* can’t initialize v1, v2;
	* default constructor for b calls default constructors for v1, v2, which doesn’t exist

```cpp
class Basis {
	Vec v1, v2;

public:
	Basis(): v1 {1,0}, v2 {0,1} {}
};
```
`{1,0}` means initialize `v1` to `Vec {1,0}`

Embedding one object (e.g. `Vec`) inside another (`Basis`) is called **composition**
Relationship: A `Basis` “owns a” `Vec`
If A “owns a” B, then typically
	* B has no identity outside A (no independent existence)
	* If A is destroyed, B is destroyed
	* If A is copied, B is copied (deep copy)
- - - -
### Example
* A car owns four wheels, a wheel is part of a car
* destroy the car => destroy the wheels
* copy the car => copy the wheels

Implementation - usually as composition of classes
Modelling:
![](Lecture%2014/Screen%20Shot%202018-10-25%20at%2011.47.22%20AM.png)
A <>====> B means A owns some number of B’s
	* can annotate with multiplicities, field names
- - - -
## Aggregation
Compare car parts in a car (“owns a”) vs car parts in a catalogue
The catalogue contains the parts, but the parts have an independent existence.
“has a” relationship (aggregation)
If A “has a” B, then typically
	* B exists apart from its association with A
	* If A is destroyed, B lives on
	* If A is copied, B is not (shallow copy)
		* copies of A share the same BB
- - - -
### Example
Parts in a catalogue, ducks in a pond
UML:
![](Lecture%2014/Screen%20Shot%202018-10-25%20at%2011.57.35%20AM.png)
Typical Implementation: pointer fields
```cpp
class Pond {
	Duck *duck[maxDucks];
	...
};
```
- - - -
## Inheritance (specialization)
Supposed you want to track your collection of books
```cpp
class Book {
	string title, author;
	int length;

public:
	Book (...);
	...
};
```

For textbooks, also want the topic:
```cpp
class Text {
	string title, author;
	int length;
	string topics

public:
	Text (...);
	...
};
```

For comic books, want the hero:
![](Lecture%2014/Screen%20Shot%202018-10-25%20at%2012.03.18%20PM.png)

This is OK - but it doesn’t capture the relationships among Book, Text, Comic
And how do we create an array (or linked list) that contains a mixture of these?

Could use…
1. Use a union
```c
union BookTypes { Book *b, Test *t, Comic *c };
BookTypes myBooks[20];
```
2. Array of `void*` - pointer to everything
still can’t tell which book type is which

Not good solutions - not type-safe
Rather observer - `Text` and `Comic` are kinds of `Book`s
`Book`s with extra feature
- - - -
### To model in c++ - inheritance 
```cpp
class Book {
	string title, author;
	int length;
public:
	Book (...);
	...
};

class Text : public Book {
	string topic;
public:
	Text (...);
	...
};

class Comic : public Book {
	string hero;
public:
	Comic (...);
};
```
`Book` is known as the **base class** (or superclass)
`Text` and `Comic` are known as **derived classes** (or subclasses)

Derived classes **inherit** fields and methods from the base class
So `Text` and `Comic` get `title`, `author`, `length` fields
Any method that can be called on `Book` can be called on `Text`, `Comic`
- - - -
### Who can see these members?
`title`, `author`, `length` - private in `Book`, outsiders can’t see them

Can `Text`, `Comic` see them?
**NO** - even subclasses can’t see them

How do we initialize `Text`?
Need title, author, length, topic
```cpp
class Text : public Book {
	string topic;
public:
	Text (string title, string author, int length, string topic):
		title { title }, author { author }, length { length }, topic { topic } {}
	...
};
```
**This is wrong for 2 reasons**
1. title, etc. not accessible in `Text`
2. Once again, when an object is created
	1. Space is allocated
	2. Superclass part is constructed
	3. Fields constructed
	4. Constructor body runs

And (2) doesn’t work - Book has no default constructor
Fix: invoke `Book`’s constructor in `Text`’s MIL
- - - -
### Subclass constructor
```cpp
class Text : public Book {
	string topic;
public:
	Text (string title, string number, int length, string topic):
		Book {title, author, length}, topic {topic} {}
};
```
`Book {title, author length}` is step (2)
`topic {topic}` is step (3)
`{}` is step (4)

If the superclass has no default constructor, subclass **MUST** invoke a superclass constructor in its MIL
- - - -
### Good reasons to keep superclass fields inaccessible to subclasses
If you want to give the subclass access to certain members
	* use **protected** visibility

```cpp
class Book {
protected:
	string title, author;
	int length;
public:
	...
};
```
`protected` - accessible only to `Book` and its subclasses

```cpp
class Text : public Book {
	...
public:
	...
	void addAuthor(string a) { author += a; }
};
```
This is OK because author field is protected so `Text` can access it
Not a good idea to give subclasses unlimited access to fields

**Better** - keep fields private, provide protected accessors/mutators
```cpp
class Book {
	string title, author;
	int length;
protected:
	string getTitle() const;
	void getAuthor(string newAuthor);
public:
	Book(...);
	bool isHeavy() const;
};	
```
subclasses can call the protected methods

Relationships among `Text`, `Comic`, `Book` is called a “is a” relationship
	* A `Text` is a `Book`
	* A `Comic` is a `Book`
![](Lecture%2014/Screen%20Shot%202018-10-25%20at%2012.49.28%20PM.png)
- - - -