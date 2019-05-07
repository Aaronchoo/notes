# Lecture 18
#UW/f18/cs246

A handler can act as a catch-all:
```cpp
try { ... }
catch(...) { // ... catches all exceptions
	...
}
```
You can throw anything you want - don’t have to throw objects
```cpp
class BadInput{}; // writing your own exception class
...
throw BadInput{};
```
when new fails: throws `std::bad_alloc`
**NEVER**
* let a destructor throw an exception
* program will abort immediately
* if you want to let a destructor throw, you can log it with `noexcept(false)`
* But if a destructor is running during stack unwinding, while dealing with another exception, and it throws, you now have two active, unhandled exceptions, and the program will abort immediately
Much more on exceptions later
- - - -
## Design Pattern - Observer Pattern
### Public/Subscribe Model
One class: publisher/subject - generates data
>= 1 subscribers/observer classes - receive data and react to it
### Example
subject - spreadsheet cells
observer - graph
when cells change, graph updates
can be many different kinds of observer objects - subject should not need to know all the details
### Observer Pattern
![](Lecture%2018/Screen%20Shot%202018-11-08%20at%2012.04.52%20PM.png)
### Sequence of method calls
1. Subjects state is updated
2. `Subject::notifyObservers()` - calls each observer’s `notify`
3. Each observer calls `Concrete Subject::getState` to query the state and reacts accordingly
### Example
* horse races
* `Subject` - publishes winners
* `Observer` - individual bettors - declare victory when their horse wins
```cpp
class Subject {
	vector<Observer *> observers;
public:
	void attach(Observer *ob) { observers.emplace_back(ob); }
	void detach(Observer *ob); // remove from observers
	void notifyObservers() {
		for (auto &ob:observers) {
			ob->notify();
		}
	}
	virtual ~Subject() = 0;
};
Subject::~Subject() {}

class Observer {
public:
	virtual void notify() = 0;
	virtual ~Observer() {}
};

class HorseRace::public Subject {
	ifstream in; // source of data
	string lastWinner;
public:
	HorseRace(string source): in { source } {}
	bool runRace() {
		in >> lastWinner;
		return in;
	}
	string getState() { return lastWinner; }
};

class Bettor::public Observer() {
	HorseRace *subject;
	string name, myHorse;
public:
	Bettor( ... ): ... {
		subject->attach(this);
	}
	~Bettor() {
		subject->detach(this);
	}
	void notify() {
		string winner = subject->getState();
		if (winner == myHorse) cout << "Win!";
		else cout << ":(";
	}
};

int main() {
	HorseRace hr;
	Bettor Larry(&hr, "Larry", "RunsLIkeACow");
	... // other bettors
	while (hr.runRace()) {
		hr.notifyObservers();
	}
}
```
- - - -
## Decorator Pattern
want to add_remove functionality to_from an object at runtime
### Example
windowing system
	* start with basic window
	* add scroll bar
	* add menu
want to choose these enhancements at runtime
![](Lecture%2018/Screen%20Shot%202018-11-08%20at%2012.34.21%20PM.png)
Component
	* interface
	* operations your objects will provide
Concrete Component
	* implements the interface - basic object
Decorators
	* all inherit from Decorator, which inherits from Component
	* every Decorator is a Component and has a Component
	* Window with scrollbar is a window, has a pointer to the underlying plain window
	* Window with scrollbar and menu is a window, has a pointer to window with scroll bar which has a pointer to window
	* All inherit from Abstract Window, so Window methods can be used polymorphically on all of them.
### Example - Pizza
![](Lecture%2018/Screen%20Shot%202018-11-08%20at%2012.47.32%20PM.png)
```cpp
class Pizza {
public:
	virtual float price() const = 0;
	virtual string desc() const = 0;
	virtual ~Pizza() {}
};

class CrustAndSauce:public Pizza {
public:
	float price() const override { return 5.99; }
	string desc() const override { return "Pizza"; }
};

class Decorator:public Pizza {
protected:
	Pizza *component;
public:
	Decorator(Pizza *p): component {p} {}
	virtual ~Decorator() { delete component; }
};

class StuffedCrust:public Decorator {
public:
	float price() { return component->price() + 2.69; }
	string desc() { return component->desc() + "with stuffed crust"; }
};
```