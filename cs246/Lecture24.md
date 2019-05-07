# Lecture24
#UW/f18/cs246
## Multiple Inheritance
A class can inherit more than one class
```cpp
class A { int a; };
class B { int b; };
class C:public A, public B { ... };
// has fields a and b
```
![](Lecture24/Screen%20Shot%202018-11-29%20at%2011.32.23%20AM.png)
Challenge
![](Lecture24/Screen%20Shot%202018-11-29%20at%2011.33.57%20AM.png)
```cpp
D d;
d.a; // which a is this?
```
ambiguous, need to say `d.B::a` or `d.C::a`
should there be 2 A parts?
what if we want (“deadly diamond”)
![](Lecture24/Screen%20Shot%202018-11-29%20at%2011.35.25%20AM.png)
make A a **virtual base class** (virtual inheritance)
```cpp
class B:virtual public A { ... };
class C:virtual public A { ... };
```
How will this laid out?
![](Lecture24/Screen%20Shot%202018-11-29%20at%2011.40.01%20AM.png) 
Same pointer should look like an `A*`,`B*`,`C*`,`D*`
doesn’t look like a `C*`
what does g++ do?

![](Lecture24/Screen%20Shot%202018-11-29%20at%2011.47.17%20AM.png)
B needs to be laid out so we can find its A part - but the distance to the A part is not always the same
solution: location of the base class object is stored in the tables
diagram doesn’t look like all of A,B,C,D simultaneously, but slices of it to look like A,B,C,D
thus pointer assigned among A,B,C,D changes the address stored in the pointer
```cpp
D *d = new D;
A *a = d;
```
d’s value shifted to point to the A part when assigning into A
static_cast/dynamic_cast will make this adjustment, reinterpret_cast will not
- - - -
### Template Functions
```cpp
template<typename T> T min(T x, T y) { return x < y ? x : y; }

int x = 1, y = 2;
int z = min(1,2);
// T = int - compiler figures this out, bsaed on the types of x and y, don't have to say min<int>(x,y)

auto f = min(1.0,3.0) // T = double
```
min works for any type T that has operator<
c++ has STC library <algorithm> - suite of template functions, many of which work over iterators
```cpp
template<typename Iter, typename T>
Iter find(Iter first, Iter last, const T &val) {
	while (first != last) {
		if (*first == val) return first;
		++first;
	}
	return last;
}
```
count - like find, but returns the # occurrences of val
```cpp
template<typename InIter, typename OutIter>
OutIter copy(InIter first, InIter last, OutIter result) {
	// copies one container range (first, last) to another, starting at result
	// note: does not allocate new memory
}

vector<int> v {1,2,3,4,5,6,7};
vector<int> w (4); // 0 0 0 0
copy(v.begin()+1, v.begin()+5, w.begin());
// w = 2 3 4 5

template<typename InIter, typename OutIter, typename Func>
OutIter transform(InIter first, InIter last, OutIter result, Func f) {
	while (first != last) {
		*result = f(*start);
		++first;
		++result;
	}
	return result;
}

int add1(int n) { return n+1; }
...
vector v {2,3,5,7,11};
vector w (v.size()); // 0 0 0 0 0
transform(v.begin(), v.end(), w.begin(), add1); // w = 3,4,6,8,12
```
how general is this code?
1. What can we use for Func?
	* how is f used?
	* f can be anything that can be called as a function
	* can write operator() for objects
```cpp
class Plus1 {
	public:
		int operator()(int n) { return n+1; }
};
Plus1 p;
p(4); // produces 5

transform(v.begin(), v.end(), w.begin(), Plus1{}); // constructor call
// Generalize
class Plus {
		int m;
	public:
		Plus(int m): m {m} {}
		int operator()(int n) { return n+m; }
};
transform(v.begin(), v.end(), w.begin(), Plus1{1}); // called function objects

// advantages of classes - can maintain state
class IncreasingPlus {
		int m = 0;
	public:
		int operator() { return n+(m++); }
		void reset() { m = 0; }
};
vector<int> v (5, 0); // 0 0 0 0 0
vector<int> w = v; // 0 0 0 0 0
transform(v.begin(), v.end(), w.begin(), IncreasingPlus{}); // w = 0 1 2 3 4
```
consider: how many ints in a vector v are even?
```cpp
vector<int> v ...;
bool even(int n) { return n%2 == 0; }
int x = count_if(v.begin(), v.end(), even);
```
seems a waste to explicitly create a function even
in racket we would use a lambda
```cpp
int x = count_if(v.begin(), v.end(), [](int n) { return n%2 == 0; }); // [] is lambda in c++
```
2. What can we use for InIter/OutIter?
```cpp
#include <iterator>

vector<int> v {1,2,3,4,5};
ostream_iterator<int> out { cout, ", " };
copy(v.begin(), v.end(), out); // prints 1, 2, 3, 4, 5, 

vector<int> v {1,2,3,4,5};
vector w;
copy(v.begin(), v.end(), w.begin());
// w doesn't have space allocated so this is illegal
```
remember - copy doesn’t allocate space in w, it doesn’t even know what w is iterating over a vector
it doesn’t even know about w at all, it only has an iterator
but if we had an iterator whose assignment operator inserts a new item?
```cpp
copy(v.begin(), v.end(), back_inserter(w));
// copies v to the end of w, adding new entries
// available for any container with a push_back method
```