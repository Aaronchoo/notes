# Lecture 5
#UW/f18/cs246

### Example
Read all ints from stdin and echo one per line to stdout. Stop on bbad input or EOF.
```cpp
int main() {
	int i;
	while (true) {
		cin >> i;
		if (cin.fail()) break;
		cout << i << endl;
	}
}
```
**Note**
	* There is an implicit conversion from `cin` to `bool`
	* lets `cin` be used as a condition
```cpp
int main() {
	int i;
	while (true) {
		cin >> i;
		if (!cin) break;
		cout << i << endl;
	}
}
```
**Note**
	* `>>` is C’s right bit shift operator
	* `a >> b` shifts `a`’s bit to the right by `b` spots
	* e.g. `21 >> 3`
			* `21` in binary is `10101`
			* becomes `10` or `2` in decimal
	* But when the LHS is `cin`, `>>` is the “get from” operator
- - - -
### Operator `>>`
	* inputs:
		* `cin` (istream)
		* data (several possible types)
	* output:
		* returns `cin` back to you (istream)

This is why we can do `cin >> x >> y >> x`
evaluates `cin >> x` and returns `cin` which can perform `cin >> y` and so on
- - - -
### Example - version 3
```cpp
int main() {
	int i;
	while (true) {
		if (!(cin >> i)) break;
		cout << i << endl;
	}
}
```
- - - -
### Example -version 4
```cpp
int main() {
	int i;
	while (cin >> i) {
		cout << i << endl;
	}
}
```
- - - -
### Example
Read all ints and echo to stdout until EOF, skip non-integer input
```cpp
int main() {
	int i;
	while (true) {
		if (!(cin >> i)) {
			if (cin.eof()) break;
			cin.clear(); // clears failure flags
			cin.ignore(); // skips the next character
		} else {
			cout << i << endl;
		}
	}
}
```
- - - -
### Notes about printing
`cout << 95 << endl;`  prints 95

what we want to print a number in hexadecimal?
`cout << hex << 95 << endl;`
`hex` is an I/O manipulator
	* all subsequent integers are printed in hex
`cout << dec;` go back to decimal

other manipulators
	* look up `#include <iomanip>`
- - - -
## Strings
### In C
	* array of characters (`char *` or `char[]`) terminated by `\0`
	* must explicitly manage memory
	* allocate more memory as strings get larger
	* easy to overwrite `\0` and corrupt memory
- - - -
### In C++
	* `#include <string>` type `std::string`
	* grow as needed (no need to manage memory)
	* safer to manipulate
- - - -
### Example
`string s = “hello";`
`”hello"` is still a C-style string (char array) - `[h,e,l,l,o]`
`s` is a c++ string created from the c string on initialization 
- - - -
### String operations
* Equality / inequality
	* `s1 == s2` or `s1 != s2`
* Comparison
	* `s1 <= s2` (lexicographic)
* Length
	* `s.length()` O(1) time complexity
* Get individual characters
	* `s[0], s[1], s[2], etc` returns a character
* Concat
	* `s3 = s1 + s2`
	* `s3 += s4`
- - - -
### Example
```cpp
int main() {
	string s;
	// Read string
	// - skips leading whitespace
	// - stops at whitespace (reads one word)
	cin >> s;
	cout << s << endl;
}
```
what if we want the whitespace?
`getline(cin ,s)` 
	* reads from current position to next new line into s
stream abstraction applies to other sources of data
- - - -
### Files
* read from a file instead of stdin
* `std::ifstream` to read from a file
* `std::ofstream` to write to a file

file access in C:
```c
#include <stdio.h>
int main() {
	char s[256];
	FILE *f = fopen("file.txt", "r");
	while (true) {
		fscanf(f, "%255s", s);
		if (feof(f)) break;
		printf("%s\n", s);
	}
	fclose(f);
}
```

**file access in C++**
```cpp
#include <iostream>
#include <fstream>
#include <string>

using namespace std;

int main() {
	// Declaring and initializing an ifstream opens the file
	ifstream f {"file.txt"};
	string s;
	while (f >> s) {
		cout << s << endl;
	}
	// the file is closed when the ifstream(f) goes out of scope
}
```
Anything you can do with `cin/cout` you can do with an `ifstream/ofstream`
- - - -
### Strings
* attach a stream to a string and use it to read from or write to the string
* `#include <sstream>`
	* `std::istringstream` read from a string
	* `std::ostringstream` write to a string
- - - -
### Example
Convert number to string
```cpp
string intToString(int n) {
	ostringostream sock;
	sock << n;
	return sock.str();
}
```

Convert string to number
```cpp
int n;
while (true) {
	cout << "Enter a number:" << endl;
	string s;
	cin >> s;
	istringstream sock {s}
	if (sock >> n) break;
	cout << "I said,";
}
cout << "You entered" << endl;
```