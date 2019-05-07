# Lecture 1
#UW/f18/cs246

**Brad Lushman DC 3110 bmlushma@uwaterloo.ca**

* Assignments - 40% (5, 7, 7, 9, 12)
* Midterm - 20%
* Final - 40%

Download XQuartz - XWindows server

- - - -
### Modules
1. Linux shell
2. C++
3. Tools
4. Software Engineering

### Homework
- [ ] Print Linux handout from Piazza, bring to class.

- - - -
## Module 1: Linux Shell
### Shell
* interface to the OS
	* get OS to run programs, manage files, etc.
* graphical
	* click/touch
* command-line
	* type commands at a prompt
	* more versatile
	
**This course: bash (command line)**

### Files, Input, Output
**cat** - displays the contents of a file
`cat /usr/share/dict/words`

In Linux, a directory is just a special kind of file.
In Linux, every line of a valid text file must end with a newline character, _INCLUDING THE LAST ONE!_
Marmoset checks for this

`^c` (i.e. CTRL-C) to stop (kills the program)
`^d` (i.e. CTRL-D) at the beginning of line sends end-of-file (EOF)
`ls` lists files in the current directory (non-hidden files)
`ls -a` lists all files in the current directory (including hidden)
`pwd` prints current directory (present working directory)
`wc` word count (lines, words, characters)

**What if you just type cat?**
* nothings happens
* cat is waiting for input
* type something
* prints everything you type

**Useful? Maybe, if we can capture the output in a file**
* `cat > myfile.txt`
* does not print user’s input
* the inputs are written to `myfile.txt`
* `cat -n > myfile2.txt`
* `-n` attaches line numbers to the input

**In general:**
* `command args > file`
* executes command args and captures the output in file instead of sending to the screen
* this is called _output redirection_

**Can also redirect input**
* `cat < file`
* takes input from file instead of keyboard
* displays file
* seems equivalent to `cat file`

**What’s the difference (important)**
* `cat file`
	* passes the name file as an argument to `cat`
	* `cat` opens the file and displays it
* `cat < file`
	* the shell opens the file and passes the contents to `cat` in place of keyboard input

**Example**
* `wc sample.txt` outputs `number number number sample.txt`
* `wc < sample.txt` outputs `number number number`
* `cat *.txt` lists all files with `.txt` extension
	* `*.txt` is called a _globbing pattern_
	* `*` match any sequence of characters
	* shell finds all files in the current directory that matches the pattern and substitutes them onto the command line
	* transforms into `cat myfile.txt myfile2.txt`
	* `cat` opens each and displays (concatenates)

**More globbing patterns - Linux sheet**
* can do both input + output redirection:
* `cat < in > out`
* take characters out of in and send them to out
* copies the file
* `cat -n < in > out`
* copies the file with line numbers attachted

**Every process is attached to 3 streams**
* Program is attached to:
	* stdin
	* stdout
	* stderr
* By default:
	* stdin is keyboard
	* stdout and stderr are the screen
* Input Redirection `<`
	* connects stdin to a file
* Output Redirection `>`
	* connects stdout to a file
* stderr
	* separate output stream for error messages
	* so output and error can go to different place
	* so that error messages don’t corrupt output formatting