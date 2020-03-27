# Udemy_Shellcode_Practice 
There are something I do not know before:
## Section3_User and Account Creation
1. uptime - check how long the host run.
2. in shell:
    * #!/bin/bash -> tell the kernel to use bash to parse the script.
    * '' - raw text.
    * "" - variable expansion.
    * ${VAR} - using {} to append text after the variable.
3. UID for root is always 0.
4. exit status -> 0 - successful, 1+ for fail.
5. ${?} is value of exit status of the last command .
6. in if:
    * = - exact match.
    * == - pattern match.
    * [] - old sytle, shell build-in.
    * [[]] - bash specific, replace [].
7. useradd config file -> /etc/login.def.
8. `man bash` and `help test` will help a lot.
9. Pattern Matching
|Pattern|Description|
|---|---|
|\*	|Match zero or more characters|
|?	|Match any single character|
|[...]	|Match any of the characters in a set|
|?(patterns)	|Match zero or one occurrences of the patterns (extglob)|
|\*(patterns)	|Match zero or more occurrences of the patterns (extglob)|
|+(patterns)	|Match one or more occurrences of the patterns (extglob)|
|@(patterns)	|Match one occurrence of the patterns (extglob)|
|!(patterns)	|Match anything that doesn't match one of the patterns (extglob)|
## Section4_Password Generation and Shell Script Arguments
1. !v - execute the most recent command that started with a v.
2. /usr/bin/\*sum - check the hash command.
3. head -c 1 <filename> -> print first byte of a file.
4. shuf - generate random permutations.
5. fold - wrap each input line to fit in specified width.
    * fold -b1 <file> - print 1 byte a line.
6. postional parameters - contain the contents of the command line.
    * ${0} -> command content.
    * ${1} or 2,3... -> argument 1, 2, 3...
    * ${#} -> number of parameters.
    * ${?} -> the value of exit status of the last command.
    * ${@} -> treat arguments as a list.
    * ${\*} -> treat arguments as a single word with the value of each parameter separated by the first variable of IFS(Internal Field Separator).
        * IFS unset - space.
        * IFS = null - with intervening separators.
7. hash - display program locations.
    * hash -r -> forget all remembered locations.
8. basename & dirname.
    * basename - return the filename from a directory.
    * dirname - return the directory name.
9. shift - Shift positional parameters -> using when user enter a comment, combine with ${@}.
## Section5_I/O Redirection
1. > STDOUT to a file.
2. < STDIN to a program -> read LINE < ${FILE} : usally read LINE form command line, you have to input something, but for now, u can use a FILE as the input source.
3. FD(file descriptor)
    * FD 0 - STDIN -> e.g. read X 0< /etc/centos-release; `0< there is no space between 0 and <` 
    * FD 1 - STDOUT -> e.g. echo "${UID}" 1> uid
    * FD 2 - STDERR -> e.g. head -n1 /fakefile 2> head.err : redirect STDERR to a file, but can not through a |(pipe), using 2>&1 to redirect STDERR to STDOUT
        * head -n1 /etc/passwd /etc/hosts /fakefile 2>&1 | cat -n
        * same to: head -n1 /etc/passwd /etc/hosts /fakefile |& cat -n
    * `head -n1 /etc/passwd /etc/hosts /fakefile > head.both 2>&1` -> 2>&1 : 
        * if 2 > file : if redirect to a normal file, without &
        * if you want to use fd, using a & to claim that it is a fd
        * simple way to do the same thing -> head -n1 /etc/passwd /etc/hosts /fakefile &> head.both
    * 1>&2 STDOUT to STDERR : when using exit 1, and before exit 1, using echo to print some text, which is STDOUT originally, but we could redirect it to STDERR using 2>&1
    * > dev/null -> black hole; 2> dev/null -> STDERR to black hole; &> dev/null -> STDOUT and STDERR to black hole
## Section6_Parsing Command Line Option

## Section10_What is Shell Script & Why we use it?
1. Process Automation: when you need to enter `multiple shell commands` AND you will need to do it again in the future.
2. Save a lot time: A script can perform a task much faster than a human can.
3. Reduce the chance of errors.
4. Speed of development.
5. Do not have to worry about low-level object, such as Bytes & Pointer.
6. easy to learn.
