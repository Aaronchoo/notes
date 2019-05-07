# Lecture 4
#UW/f18/cs246

## More Bash Scripts
### Recall payday
``` bash
#!/bin/bash
report() {
	if [ $1 -eq 31 ]; then
		echo "This month: the 31st"
	else
		echo "This month: the ${1}th"
	fi
}
report $(cal | awk '{print $6}' | egrep "[0-9]" | tail -1)
```
- - - -
### Generalize to any month
`cal October 2018` gives the calendar for October 2018
Let payday October 2018 give October 2018’s payday

`report $(cal $1 $2 | awk '{print $6}' | egrep "[0-9]" | tail -1) $1`

if $1 and $2 are empty, then it reverts to previous behaviour to just `cal`

``` bash
#!/bin/bash
report() {
	if [ $2 ]; then # $2 is the month, if it is not blank
		# -n means don't print a new line
		echo -n $2
	else
		echo -n "This month"
	fi
	if [ $1 -eq 31 ]; then
		echo ": the 31st"
	else
		echo ": the ${1}th"
	fi
}
report $(cal $1 $2 | awk '{print $6}' | egrep "[0-9]" | tail -1) $1
```
- - - -
## SE Topic: Testing
### Overview
* essential part of program development
* ongoing
	* begins before you start coding
		* test suites
		* expected behaviour
	* continues while you are coding
* debugging
	* must test first
	* cannot guarantee correctness
		* can only prove wrongness
* ideally programmer != tester (not in this course)
* is hard
	* no general formula
	* psychological barrier
		* don’t want to find out your program is wrong
* human testing
	* humans look over code, find flaws
	* code inspections
* machine testing
	* run program on selected inputs, compare with expected output
	* can’t check everything, choose carefully
* black / white / grey box testing
	* no / full / some knowledge of program implementation 
* start with black box, supplement with white box
	* various classes of input
		* numeric ranges
		* positive vs negative
	* boundaries of valid data ranges (edge cases)
	* multiple simultaneous boundaries (corner cases)
	* intuition / experience
		* guess at likely errors
		* extreme cases (within reason)
* white box
	* execute all logical paths through the program
	* make sure every function runs
* **DO NOT TEST**
	* invalid input, unless a behaviour has been prescribed
	* if input is invalid, there is no corresponding correct output, so any such test case would be meaningless
* regression testing
	* make sure new changes to the program don’t break old test cases
	* test suites or testing scripts always add tests, never subtract
- - - -
## Module 2 - C++
### Hello World in C
``` c
#include <stdio.h>

int main() {
	printf("Hello world\n");
}
```
- - - -
### Hello World in C++
``` cpp
#include <iostream>
using namespace std;

int main () {
	cout << "Hello world" << endl; 
}
```
**Notes**
	* `void main() {…}` is illegal in c++
	* implicit return 0 if not specified (`echo $?`)
	* c version still legal in c++
	* preferred c++ I/O
		* header `<iostream>`
		* `std::cout << __ << __ << __ ...`
		* `std::end` = end of line
	* `using namespace std;`
		* let’s you omit the `std::` prefix
- - - -
### Compiling C++ Programs
`g++ -std=c++14 -Wall program.cc -o program`
OR
`g++14 program.cc -o program`

`-o program` sets output to `program`, if not set then defaulted to `a.out`
`Wall` stands for warn all, and turns on compiler warnings
- - - -
### Input/Output
**3 I/O streams**
	* cout / cerr
		* for printing to stdout / stderr
	* cin
		* for reading from stdin

**I/O operators**
	* `<<` “put to” (output)
	* `>>` “get from” (input)
	* `cerr << x`
	* `cin >> x`
	* operator “points” in the direction of information flow
- - - -
### Example - add two numbers
``` cpp
#include <iostream>
using namespace std;
// going to omit the above two lines from now on

int main() {
	int x, y;
	cin >> x >> y;
	cout << x + y << endl;
}
```

**Note**
	* `cin` ignores whitespace
		* space
		* tab
		* newline

What if input doesn’t contain an integer next?
What if input integer is too large or too small?
What if input is exhausted before we get 2 ints?
	* statement fails

If the read failed
	* `cin.fail()` will be true
If EOF
	* `cin.eof()` and `cin.fail()` will both be true
	* but not until the attempted read fails
- - - -
### Example
Read all ints and echo them, one per line, to stdout
Stop on bad input or EOF
