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
8. `man/help bash` | `man/help test` | `man/help logger` | `help set` will help a lot.
    * [test_command](https://www.computerhope.com/unix/test.htm)
9. `type -a bash` is also help.
10. Pattern Matching

| Pattern      | Description                                                     |
|--------------|-----------------------------------------------------------------|
| \*           | Match zero or more characters                                   |
| ?            | Match any single character                                      |
| [...]        | Match any of the characters in a set                            |
| ?(patterns)  | Match zero or one occurrences of the patterns (extglob)         |
| \*(patterns) | Match zero or more occurrences of the patterns (extglob)        |
| +(patterns)  | Match one or more occurrences of the patterns (extglob)         |
| @(patterns)  | Match one occurrence of the patterns (extglob)                  |
| !(patterns)  | Match anything that doesn't match one of the patterns (extglob) |
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
9. shift - Shift positional parameters -> using when user enter a comment, combine with ${@}. OR remove options and remain arguments.
## Section5_I/O Redirection
1. \> STDOUT to a file.
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
    * \> dev/null -> black hole; 2> dev/null -> STDERR to black hole; &> dev/null -> STDOUT and STDERR to black hole
## Section6_Parsing Command Line Option
### Parsing
1. using case to list options. 
2. some KEY_WORD:
    * local - local variable in function
    * readonly - constant variable in shell
3. logger "hello from command line." - write things to /var/log/messages.
    * logger -t my-script 'Tagging on' - give record a tag to identify it.
4. /tmp vs. /var/tmp
    * /tmp - clear on reboot, in running system - clear 10 days(CentOS7)
    * /var/tmp - will not be cleared on reboot, in running system - clear 30 days(CentOS7)
5. $$ - PID of the script.
6. cp -p file1 file2 - preserves the file's mode ownership timestamp
7. funtion in shell will return the exit status of the `last command` it executes
8. data +%F-%N -> %F - Full date; %N - nanosecond(timestamp)
9. getopts - parse arguments
    * OPTION - store command line arguments
    * OPTIND - store the position of the next command line argument following the options in that variable
10. $(( )) -> arithmetic expansion -> e.g. $(( 1 + 2 )) -> just decimal, no rounding
11. bc - a commond to round numbers -> e.g. bc -l
12. let - Evaluate arithmetic expressions -> man let -> let NUM='2 + 3' same as NUM=$(( 2 + 3 ))
13. expr -> NUM=$(expr 2 + 3 ) same as NUM=$(( 2 + 3 ))
### Del user
1. !! - most recently executed command
2. find - if you do not give a path, the default is your current directory. [find command details](https://jaywcjlove.gitee.io/linux-command/c/find.html)
3. userdel <user_name> - delete user
3. id -u root -> 0 , system account have lower UID
4. uid configuration -> /etc/login.defs
5. tar - archive files
    * tar -cf <file_path> <path> - create archive file
    * tar -tf <file_path> - list .tar file content
    * tar -xf <file_path> - extract file
6. gzip catvideos.tar / gunzip catvideos.tar.gz - compress/uncompress tar file -> same as tar -zcvf catvideos.tar.gz
    * tar -ztvf <file_path> - list tar.gz file content
7. cd / ls -l !$ - cd or ls last path(Actually, $ store the last argument)[shell_command_!$](https://unix.stackexchange.com/questions/254568/shell-command-cd)
### Disable/block Account
1. chage - change user password expiry information
    * sudo chage -E 0 <account_name> - block account; old school method: sudo passwd -l <account_name> (not work when user use ssh_key to login, so recommand to use chage)
    * sudo chage -E 1 <account_name> - unblock account; old school method: sudo passwd -u <account_name>(not work when user use ssh_key to login, so recommand to use chage)
2. cat /etc/shells - list available shells
3. sudo usermod -s /sbin/nologin <account_name> to block a user
4. mkdir -p : if parent path not exist, create it.
## Section7_Text Manipulation
1. cut -c 1,3,5 <file_name>
    * -b -> cut by byte
    * -d -> assign delimiter
    * -f -> cut by field, default delimiter is Tab(/t)
    * --output-delimiter=',' -> set output delimiter
    * NOT support -d 'DATA:' -> complicate delimiter -> using AWK
2. grep -v 'first,last' -> -v reverse search 
3. awk
    * awk -F 'DATA:' '{print $2}' people.data
    * awk -v -> set awk variable e.g. awk -v OFS=','(OFS - output field delimiter), default=(white_space)
    * awk -F ':' '{print $1, $3}' /etc/passwd
        * '{print $3, $1}' -> change print order
        * '{print $1 ", " $3}' -> using ,(space) to seperate 
        * '{print "COL1: " $1 "COL1: " $3}' 
        * $NF - number of the fields
4. netstat -4nutl -> just output tcp4
5. sort - sort output
    * sort -r - reverse order
    * sort -nr - order by number
    * sort -u - unique output
    * sort -t: -k 3 -n -> -k field name to sort
6. uniq - output has to be sorted
    * uniq -c - count the occurrences
7. du -h /var - check size of file or directory
8. geoiplookup <ip> - check geographic location of the ip
### Sed - Stream editor
1. sed 's/search-pattern/replacement-string/**flags**' <file>
2. sed 's/text(re, actually)/sub_text/' manager.txt
3. sed 's/text(re, actually)/sub_text/i(I)' manager.txt -> ignore case, insensitive match
4. by default, sed replace the first match. using flag -> g to do global replace
5. sed 's/text(re, actually)/sub_text/2' manager.txt -> just replace the second match
6. sed -i.bak 's/text(re, actually)/sub_text/' manager.txt -> make a backup and write the replace into the original file
7. sed 's/text(re, actually)/sub_text/gw test.txt' manager.txt -> test.txt contain the lines contain replacement
8. sed 's#/home/jason#/home/sed#' -> using # as a delimiter
9. sed '/This/d' manager.txt -> delete the match lines
10. sed '/^$/d' manager.txt -> delete the empty lines
11. sed '/^$/d ; /^$/d ; s/apache/httpd/' config -> multiple replace, sepeate by ';' -> same as sed -e '/^$/d' -e '/^$/d' -e 's/apache/httpd/' config
12. load .sed script
    * sed -f script.sed config
```shell
# script.sed
/^$/d
/^$/d
s/apache/httpd/
```
13. sed '1,3 s/apache/httpd/' config -> replace just line 1, 3
14. sed '/Group/ s/apache/httpd/' config -> replace just the line contain Group
## Section9_Network Scripting
1. /etc/hosts - All operating systems with network support have a hosts file in order to translate hostnames to IP addresses. 
    * 10.8.9.10 server01 -> 'ping 10.8.9.10' is same as 'ping server01'
2. using ssh to connect to other hosts
    * ssh-keygen -> enter, enter(empty for no passphrase)
    * ssh-copy-id <hostname or ip> -> copy the public key to the remote system(need remote system password)
3. ssh -> exit status = 255
4. help set -> 'set -o pipefail; false | true' -> pipefail will return any none-zero value through pipe process, except every step through pipe is return 0
## Section10_What is Shell Script & Why we use it?
1. Process Automation: when you need to enter `multiple shell commands` AND you will need to do it again in the future.
2. Save a lot time: A script can perform a task much faster than a human can.
3. Reduce the chance of errors.
4. Speed of development.
5. Do not have to worry about low-level object, such as Bytes & Pointer.
6. easy to learn.
