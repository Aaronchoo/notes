# Lecture 3
#uw/f18/cs246

## Shell Scripts
* Files containing sequences of shell commands, executed as programs.
- - - -
### Example - Print date, current user, current directory
* `cat > myscript`
``` bash
#!/bin/bash - "shebang line"
# execute this file as a bash script
date
whoami
pwd
```
* then give file execute permission
* `chmod u+x myscript` 
* running `myscript` doesn’t work
* instead run `./myscript`
- - - -
### Variables
`x=1` (**NO spaces**)
`echo x` prints `x`
`echo $x` prints `1`

**Note**
	* use `$` when fetching the value of a variable
	* No `$` when setting a var
	* Good practise: use brace brackets `${x}`
	* All variables contain strings
		* e.g. `x` above contains the string `"1"`
- - - -
### Global Variables
**Important**: `PATH`
	* list of directories, separated by `:`
	* when you type a command, the shell searches these directories, in order, for a program with that name

`echo “${PATH}”` prints the `PATH` variable
`echo ‘${PATH}’` prints `${PATH}`
- - - -
### Special Variables For Scripts
`$1`, `$2`, etc
	* command line arguments
- - - -
### Example - Check whether a word is in the dictionary
expected command - `./isItAWord hello`
``` bash
#!/bin/bash
egrep "^$1$" /usr/share/dict/words
```
Prints nothing if the word is not found, prints the word if found.
- - - -
### Example - Whether or not password contains a word in the dictionary
``` bash
#!/bin/bash
egrep "^$1$" /usr/share/dict/words > /dev/null # suppresses output
if [ $? -eq 0]; then
	echo Bad password
else
	echo Maybe a good password
fi
```
**Note**
	* Every program returns a status code when finished
	* `egrep` returns `0` if found, `1` if not found
	* In linux: `0` means success and non-zero means failure
	* `$?` status of most recently executed command
- - - -
### Verify # of arguments print error msg if wrong
```bash
#!/bin/bash
usage() {
	echo "Usage: $0 password" 1>&2 # redirect stdout to stderr
	# $0 is the name of the program
}
if [ $# -ne 1 ]; then
	usage
	exit 1
fi
```
- - - -
### Loops
Print the numbers from 1 to $1
```bash
#!/bin/bash
while [ $x -le $1 ]; do
	echo $x
	x=$((x+1)) # $((...)) for arithmetic
done
```
- - - -
### Looping over a list
```bash
#!/bin/bash
for x in a b c; do
	echo $x
done
```

Rename all `.cpp` files to `.cc`
```bash
#!/bin/bash
for name in *.cpp; do # glob - replaced with all matching files
	mv ${name} ${name%cpp}cc
	# name%cpp is the value of name without the trailing cpp
done
```
- - - -
### How many times does word $1 occur in file $2
```bash
#!/bin/bash
x=0
for word in $(cat $2); do
	# the variables are in "" because they could be [ or ] which could mess up the syntax
	if [ "$word" = "$1" ]; then
		x=$((x+1))
	fi
done
echo $x
```
- - - -
### Payday is the last Friday of the month, when is this month’s payday?
**tasks**
	* compute date
	* report answer
```bash
#!/bin/bash
cal | awk '{print $6}' | egrep "[0-9]" | tail -1
```
