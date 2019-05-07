# Lecture 9
#UW/f18/cs246

```cpp
struct Student {
	int assns, mt, final;
	float grade();
};

float Student::grade() {
	return assns * 0.4 + mt * 0.2 + final * 0.4;
}
```

What do assns, mt, final mean inside of Student::grade?
	* they are fields of the current object
	* the object upon which grade was called

### Example
```cpp
Student billy {...};
billy.grade(); // method call, uses billy's assns, mt, final
```

### Formally
methods take a hidden extra parameter called this
	* pointer to the object on which the method was called

### Example
```cpp
billy.grade(); // this == &billy
```
can write
```cpp
struct Student {
	...
	float grade() {
		return this->assns * 0.4 + this->mt * 0.2 + this->final * 0.4;
	}
	/*
		methods can be written in the class.
		we will often do this for briefly
		You should put inplementations in .cc files
	*/
};
```
- - - -
## Initializing Objects
### C style struct initialization
```cpp
Student billy {70, 80, 90}; // OK, but limited
```

### Constructor - a method that initializes for us 
```cpp
struct Student {
	int assns, mt, final;
	float grade();
	Student (int assns, int mt, int final);
};

Student::Student (int assns, int mt, int final) {
	this->assns = assns;
	this->mt = mt;
	this->final = final;
}

Student billy {70, 80, 90}; // better
// If a constructor has been defined, these are passed as args to the constructor
// If no constructor was defined, these initialize the individual fields of student
// OR
Student billy = Student {70, 80, 90};

// heap allocation
Student *pBilly = new Student {70, 80, 90};
```
- - - -
### Advantages of Constructors
	* default params
	* overloading
	* sanity checks

```cpp
struct Student {
	...
	Student (int assns = 0, int mt = 0, int final = 0);
	...
};

Student jane {70, 80}; // 70, 80, 0

Student newKid; // 0, 0, 0
```
- - - -
### Note
Every class comes with a default constructor (which just default-construct all fields that are objects)
e.g.
`Vec v;`  calling the default constructor (does nothing)
But the built-in default constructor goes away if you write any constructor
- - - -
### Example
```cpp
struct Vec {
	int x, y;
	Vec (int x, int y) {
		this->x = x;
		this->y = y;
	}
}

Vec v; // does not compile
Vec v {1,2}; // OK
```
- - - -
### Struct containing constants or references
```cpp
struct MyStruct {
	// must be initialized
	const int myconst;
	int &myRef;
};
// so initialize
int z;
struct MyStruct {
	const int myConst = 5;
	int &myRef = z;
};
```
But - does every instance of `MyStruct` need to have the same value of myConst, etc?
e.g.
```cpp
struct Student {
	const int id; // constant, but not the same for all students
	...
};
```
Where do we initialize? constructor body?
	* too late
	* fields must be fully constructed by then
What happens when an object is created?
	1. Space is allocated
	2. Fields are constructed in declaration order (constructor run for fields that are objects)
	3. Constructor body runs
Need to put our initializations in Step 2
- - - -
### Member Initialization List (MIL)
```cpp
Student::Student (int id, int assns, int mt, int final):
	id {id}, assns {assns}, mt {mt}, final {final} // step 2
	{} // step 3
```
Note:
	* can initialize any field this way, not just `consts` and `refs`
	* Fields always initialized in declaration order, even if the MIL orders them differently
	* Sometimes more efficient than setting fields in the body.
		* otherwise, run default constructor in step 2, then reassign in step 3
```cpp
struct Student {
	string name;
	...
};
```
What if a field is initialized inline AND in the MIL?
```cpp
struct Vec{
	int x = 0, y = 0;
	Vec (int x): x {x} {}
};
```
MIL takes precedence
Inline field construction is only performed if the field is not mentioned in the MIL
Now consider:
```cpp
Student billy {70, 80, 90};
Student bobby = billy;
// how does this initialization happen?
// the copy constructor, for creating one object as a copy of another
```
- - - -
### Every class comes with:
	* default constructor (default-constructs all fields that are objects)
		* lost if you write any constructor
	* copy constructor (just copies all fields)
	* argument operator
	* destructor
	* move constructor
	* move assignment operator
- - - -
### Building your own copy constructor
```cpp
struct Student {
	int assns, mt, final;
	...
	Student (const Student &other):
		assns {other.assns}, mt {other.mt}, final {other.final} {}
	// equivalent to built in
};
```
When is the built-in copy constructor not correct?
Consider:
```cpp
struct Node {
	int data;
	Node *next;
}; // built-in copy constructor

// pointer on stack, linked list on heap
Node *n = new Node { 1, new Node { 2, new Node { 3, nullptr }}};

// new head node on the stack, which points to rest of the list n on heap
Node m = *n;

// pointer on stack, new head node on heap, points to rest of the list n
Node *p = new Node { *n }
```
Simple copy of fields => only the first node is actually copied (shallow copy)
If you want a deep copy (copies the whole list), must write your own constructor