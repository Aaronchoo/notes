# Lecture 2
#UW/f18/cs246

* stdout may be buffered
* system may wait to accumulate output before actually displaying (flushing) it
* stderr is never buffered
* error messages are displayed immediately

### Can also redirection stderr
* `program args < infield > outfile 2> errfile`
- - - -
## Pipes
* output of one program as input of another
* set the second program’s stdin to the first program’s stdout

### Example - How many words occur in the first 20 lines of myfile?
* Tools:
	* `head -n file`
		* gives the first n lines of the file
	* `wc -w file`
		* counts the number of words
* Solution:
	* `head -20 myfile | wc -w`
	* `|` is called a pipe
* Or:
	* `cat myfile | head - 20 | wc -w`

### Example - Suppose words1.txt, words.txt, etc. contain lists of words, one per line. Print a duplicate-free list of all words that occur in any of these
`uniq`
	* removes _adjacent_ duplicate lines
	* if entries are sorted, removes all duplicates
`sort`
	* sorts lines
solution
	* `cat words*.txt | sort | uniq`

### Example
`echo “Today is $(date) and I am $(whoami)"`
	* runs the `date` and `whoami` command then substitute into the string
careful:
`echo ‘Today is $(date) and I am $(whoami)'`
	* literally prints out the string
	* no $ expansion inside single quotes

### Pattern-matching in text files
`grep`/`egrep`
(extended) global regular expression print

`egrep pattern file`
prints every line in file that contains pattern

### Example - Print every line in myfile that contains cs246
`egrep cs246 myfile`

### Example - How many lines in myfile contain cs246 or CS246
`egrep “cs246|CS246” myfile | wc -l`
or
`egrep “(cs|CS)246” | wc -l`

### Available patterns - called regular expressions (different from globbing  patterns)
`egrep “(c|C)(s|S)246”`
	* also matches `cS246`, `Cs246`
`egrep “[cC][sS]246”`
	* `[…]` any one char between `[` and `]`
	* `[^…]` any one char except between `[` and `]`

Add optional space
`egrep [cC][sS]_?246`
	* `?` = 0 or 1 of preceding expression (0 or 1 space in this case)
	* `*` = 0 or more of the preceding expression
	* `egrep ”(cs)*246”`
		* `246`,  `cs246`, `cscs246`, `cscscs246`
	* `+` = 1 or more of preceding expression
	* `.` = any single character
	* `.*` = any string
	* `egrep ”cs.*246"`
		* lines containing `cs...(anything)...246`
	* `.+` 
		* non-blank
	* `^`, `$`
		* beginning/end of the line
		* `grep “^cs246”` - lines that start with `cs246`
		* `grep “^cs246$”` - lines that are exactly `cs246` and nothing else
### Example - lines of even length
`grep "^(..)*$"`

### Files in the current dir whose names contain exactly one a
`ls | egrep "^[^a]*a[^a]*$"`

### 5 character word that starts with e
`egrep "^e....$"`
- - - -
## Permissions
`ls -l`
	* long form listing
	* `-rw-r—— 1 j2smith j2smith 25 Sep 9 15:27 abc.txt`
	* first `-`  is type
		* `-` ordinary file
		* `d` directory
	* `rw-` user permissions (owner)
	* `r--` group permissions
	* `---` other permissions
	* first `j2smith` is owner
	* second `j2smith` is group
	* `25` is size
	* `Sep 9 15:27` is the last modified time
	* `abc.txt` is name

groups
	* a user can belong to 1 or more groups
	* a file can be associated with 1 group

user bits
	* apply to the file’s owner
group bits
	* apply to members of the file’s group (other than the owner)
other bits
	* apply to everyone else

### Bit
* `r`
	* read bit
	* file’s content can be read
	* directory’s content can be read
		* e.g. `ls`, globbing, tab completion
* `w`
	* write bit
	* file’s contents can be modified
	* directory’s contents can be modified
		* can add/remove files
* `x`
	* execute bit
	* file can be executed as a program
	* directory can be navigated
		* i.e. can `cd` into the directory
		* If directory’s execute bit is not set, there is no access at all, nor to any files/directories within it.

### Changing Permissions
`chmod mode file`
* mode
	* (u, g, o, a) (+, -, =) (r, w, x)
	* user, group, other, all
	* add permission, revoke permission, set permission exactly
	* read, write, execute
* Example - give others read permissions
	* `chmod o+r file`
* Example - make everyone’s permission `rx`
	* `chmod a=rx file`
* Example - give the owner full control
	* `chmod u+rwx file`
	* `chmod u=rwx file`


