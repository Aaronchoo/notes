# Lecture 19
#UW/f18/cs246

## Design Pattern - Factory Method Pattern
Write a video game with 2 kinds of enemies: turtles and bullets
	* system randomly sends turtles and bullets, but bullets more frequent in harder levels

![](Lecture%2019/Screen%20Shot%202018-11-13%20at%2011.38.58%20AM.png)
	* Never knew exactly which enemy comes next, so can’t call turtle/bullet constructors directly
	* Instead put a factory method in `Level` that creates enemies
```cpp
class Level {
	public:
		virtual Enemy *createEnemy() = 0; // factory method
};

class Easy:public Level {
	public:
		Enemy *createEnemy() override {
			// create mostly turtles
		}
};

class Hard:public Level {
	public:
		Enemy *createEnemy() override {
			// mostly bullets
		}
};

Level *l = ...;
Enemy *e = l->createEnemy();
```
- - - -
## Design Pattern - Template Method Pattern
* want subclasses to override superclass behaviour, but some aspects must stay the same
### Example
there are red turtles and green turtles
```cpp
class Turtle {
	private:
		void drawHead() {...}
		void drawFeet() {...}
		virtual void drawShell() = 0;

	public:
		void draw() {
			drawHead();
			drawShell();
			drawFeet();
		}
};

class RedTurtle:public Turtle {
	void drawShell() override { /* draw red shell */ }
};

class GreenTurtle:public Turtle {
	void drawShell() override { /* draw green shell */ }
}
```
* subclasses can’t change the way a turtle is drawn (head, shell, feet), but can change the way the shell is drawn
- - - -
## Generalization: the Non-Virtual Interface (NVI) Idiom
* A public virtual method is really two things (contradictory):
	* an interface to the client
		* indicate provided behaviour with pre/post conditions
	* an interface to the subclasses
		* a “hook” to insert specialized behaviour
* Hard to separate these ideas if they are tied to the same function
	* what if want to split a virtual method into two, maybe with some constant code in between? -> also changes client’s interface
	* how could you make sure overriding functions conform to the pre/post condition
### NUI says
* **all** public methods should be non-virtual
* **all** virtual methods should be private (or at least protected)
* except the destructor
### Example
```cpp
class DigitalMedia {
	public:
		virtual void play() = 0;
};
// should be
class DigitalMedia {
	public:
		// can add before and after code
		void play() {
			checkCopyright();
			doPlay();
			updatePlaycount();
		}
	private:
		virtual void doPlay() = 0;
};
```
Generalizes Template Method
	* puts **every** virtual function inside a template method
- - - -
## STL Maps - for creating dictionaries
e.g. “arrays” that map strings to int:
```cpp
#include <map>

std::map<string, int> m;
m["abc"] = 1;
m["def"] = 4;
cout << m["ghi"]  // 0
     << m["def"]; // 4
//If key is not present, it is inserted and value is default-constructed (for ints, 0)

// taking something out of a map
m.erase("abc");

if (m.count("abc")) // 0 = not found, 1 = found
```
### Iterating over a map -> sorted key order
```cpp
for (auto &p:m) {
	cout << p.first << " " << p.second << endl;
}
// p's type is std::pair<string, int> (<utility>)
// p.first and p.second are fields not methods
```
- - - -
## Design Pattern - Visitor Pattern
For implementing **double dispatch**
virtual methods - chosen based on the actual type (at runtime) of the receiving object
what if you want to choose based on two objects?
### Example
![](Lecture%2019/Screen%20Shot%202018-11-13%20at%2012.26.57%20PM.png)
want something like `virtual void (Enemy, Weapon)::strike();`
If strike is a method of Enemy (resp. Weapon) choose based on enemy (resp. weapon), but not on weapon (resp. enemy)
trick is to get dispatch on both (double dispatch) combines overriding with overloading
```cpp
class Enemy {
	public:
		virtual void beStruckBy(Weapon &w) = 0;
};

class Turtle:public Enemy {
	public:
		void beStruckBy(Weapon &w) override { w.strike(*this); }
};

class Bullet::public Enemy {
	public:
		void beStruckBy(Weapon &w) override { w.strike(*this); }
}

class Weapon {
	public:
		virtual void strike(Turtle &t) = 0;
		virtual void strike(Bullet &b) = 0;
};

class Stick:public Weapon {
	public:
		void strike(Turtle &t) override {
			// strike turtle with stick
		}
		void strike(Bullet &b) override {
			// strike bullet with stick
		}
};

class Rock // similar

Enemy *e = new Bullet {...};
Weapon *w = new Rock {...};
e->beStruckBy(*w); // what happens?
// Bullet::beStruckBy runs (virtual method), calls Weapon::strike, *this is Bullet
// thus Bullet version was chosen at compile time
// vritual method call resolves to Rock::strike(Bullet &)
```
Visitor can be used to add functionality to existing classes, without changing or recompiling the classes themselves.
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
```