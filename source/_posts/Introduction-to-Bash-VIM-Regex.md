---
title: 'Introduction to Bash, VIM & Regex'
date: 2019-10-03 12:22:59
categories: [学习笔记]
tags: [FrontEndMasters, bash, vim, regular expressions]
---
## Bash

### Introducing UNIX

+ UNIX
  + UNIX was an operating system developed at AT&T Bell Labs in the 1960s through the 1980s
  + GUN/Linux, MacOSX, and Android are all based on ideas and specifications created by UNIX

+ time-sharing
  + UNIX was orininally built for large mainframe computers that many people would use at the same time

+ terminals and teleprinters
  + teleprinters printed program output on paper
  + terminals displayed output on a CRT monitor
  + neither device had processing power of their own
  + connected to the mainframe over cables or by telephone
<!-- more -->
+ teletype legacy: standard input and output
  + Every program on a UNIX system can read input from the standard input device(stdin) and write to standard output(stdout)
  + By default, stdin comes from the keyboard and stdout gets "printed" to the graphical display

+ organization
  + The UNIX operating system is a collection of programs, each with a special role:
    + kernel
    + shell
    + utilities

+ kernel
  + mediate access between user programs and system resources
    + CPU scheduling
    + I/O to computer hardware
    + memory
  + programs request resources by making a syscall

### Introducing the UNIX Shell

+ shell
  + A shell is a computer program that can execute other programs from a text-based interface
  + In a text-based interface, you interact with a program completely from the command-line with text commands and text output
  + Most modern shells are strongly influenced by the first UNIX shells

+ shells through the ages
  + thompson shell - Ken Thompson 1971
  + pwb(mashey) shell - John Mashey 1975
  + bourne shell - Stephen Bourne 1977
  + c shell(csh) - Bill Joy 1978
  + tcsh - Ken Greer and Mike Ellis 1983
  + korn shell - David Korn 1983
  + bourne again shell (bash) - Brian Fox 1987
  + almquist shell(ash) - Kenneth Almquist 1989
  + debian almquist shell(dash) - Herbert Xu 1997
  + in complete list of popular or influential shells

+ utilities
  + Any distribution of UNIX will come with dozens of other programs that perform narrow single-purpose tasks
  + The available utilities on a given system vary widely but some utilities are very common
  + For example, there is a command to make new directories and another to move files

+ Why UNIX still matters
  + portable to many kinds of hardware
  + consistent conventions
  + vast softwarwe ecosystem
  + text!

+ Place you can find a unix command-line
  + wifi routers
  + dsl and cable modems
  + raspberry pi, beaglebone, nvidia jetson
  + android phones
  + linux laptop or desktop
  + Mac OSX computer
  + web server
+ you can take your command-line skills with you to all of these platforms and more

+ text interface
  + To remotely access a UNIX system, you can use the same command-line tools and interface that you use locally. You can remotely access devices with out a display
  + Text is easy to read so you can poke around more easily to figure out what's going on. Many aspects of computer programming involve shuffling text around. UNIX excels at these kinds of tasks

+ UNIX philosophy
  + The UNIX philosophy is a set of design principles for how programs relate to each other
    + each program should do one thing well
    + the output of a program can become the input of another
  + UNIX Programming Environment 1984 - Brian Kernighan and Rob Pike

### Using Shell

+ Let's learn the command line
  + bash is a popular shell for UNXI-like systems.
  + Open up a bash shell for the next sections to follow along
  + If you're not sure which shell you're in, type:
    + `echo $SHELL`
  + If you're in bash, you should see something like:
    + `/bin/bash`

+ list files
  + You can use the `ls` command to show all the files in the current directory

+ print the current directory
  + To display the current directory, you can use the `pwd` command
    + `pwd` stands for print working directory

+ change directory
  + To change the current working directory, use the `cd` command. The `cd` command takes a single argument: the directory to move to.
  + After changing the current directory, list the files again with `ls`.

### Introducing Aliases

直接在shell中输入`alias`，会看到所有依赖设置。

### Special Directories

+ There are some special directories:
  + `..` - the parent directory
  + `.` - the current directory
  + `~` - your home directory
+ To navigate back up to the parent directory, do `cd ..`

## File Management

### Combining and Copying Files

+ `cat`
  + cat was originally written to concatenate all the files from its arguments
  + but it also a handy way to display single text files on the command-line

+ `cp`
  + Copy a file to another directory or file name. You can copy a single file to make a new duplicate file

+ `cp -r`
  + If you have a directory full of files and directories you want to copy to a new place, you can use `cp -r` to resursively copy a directory and all its subdirectories to a new location

### Moving Files and Creating Directories

+ `mv`
  + The `mv` command is used to rename and overwrite files and directories
  + To rename a file, set the first argument to the original file name and the second argument to the new file name or destination directory

+ `mkdir`
  + To make a new directory. just execute the `mkdir` command with a list of new directory names to make as arguments

### Commands to Help Reduce Repeetitive Typing

+ `{}`
  + `echo {a,b}{c,d}` ac ad bc bd
  + `echo cat b{oo,ee}p.txt` cat boop.txt beep.txt

+ brace expansion sequences
  + It can be tedious to type out numerical lists by hand
  + Brace expansions can help with that

```bash
$ echo wow{1..10}
wow1 wow2 wow3 wow4 wow5 wow6 wow7 wow8 wow9 wow10
```

+ and you can even specify an amount to skip:

```bash
$ echo wow{0..100..10}
wow0 wow10 wow20 wow30 wow40 wow50 wow60 wow70 wow80 wow90 wow100
```

+ `*`
  + `echo cat b*p.txt` cat beep.txt boop.txt
  + `echo cat *.txt` cat beep.txt boop.txt

### Deleting Files and Counting Words

+ `rm`
  + To remove a file, just do
    + `rm path/to/file`
  + You can remove multiple files at once
    + `rm path/to/file path/to/another/file`
  + and you can remove entire directories inculding subdirectories with
    + `rm -r path/to/directory`
  + Be vary careful with `-r`. You might accidentally delete much more than you meant to delete

+ `wc`
  + The `wc` command computes the number of lines, words and bytes in a file
  + To see each field independently, you can use different options: arguments that start with a `-` or `--` followed by a letter or word

### Documentation and Options

+ `man`
  + All of these command options are a lot to remember!
  + You can pull up documentation at any time in your shell by typing `man foo` for any command `foo`
  + For example to read up on all the options you can give to the `wc` command, do:
    + `man wc`
  + The help page will open up in your `$PAGER`. Type `q` to exit back to your shell.

+ more on options
  + Options(also called flags or switches) are special arguments that start with a `-` or `--` followed by a letter or word
  + generally speaking, they are distinct from other auguments in that their order usually doesn't matter.For example:
    + `grep -i wow`
  + is the same as
    + `grep wow -i`
  + where `-i` just informs the `grep` command to perform a case-insensitive search

### Paths

+ absolute and relative paths
  + paths that start with `.` or `..` are relative paths
  + paths that start with `/` are absolute paths

### Writing and Reading Files

+ `echo`
  + The echo command just prints text from its arguments
  + This is not very useful by itself, but becomes useful when combined with redirects and pipes

+ write to a file
  + Using the `>` character, you can write the output of a command to a file
  + For example, to make a new file `greetings.txt` with the contents "ahoy thar", we can do
    + `echo ahoy thar > greeting.txt`
  + and to print the contents of greetings.txt, use `cat`
    + `cat greeting.txt` ahoy thar
  + you can redirect the output of any program to a file
    + `ls / > list.txt`

+ append to a file
  + The `>` redirect operator will overwrite a file with new contents if it already exits
  + This is a `>>` operator that appends to the end of a file if it already exist

+ read from a file
  + You can read a file into the stdin of a program with `<`
  + Remember that if `wc` dosen't get a file as an argument, it will read from stdin. We can load a file into `wc` with `<` instead

## Pipes

+ pipes!
  + The last but most important kind of redirect is the pipe operator `|`
  + With `|` you can feed the output of one program to the input of the next
  + For example, the `ls -1` command will list files, one per line, to stdout. The `wc -l`command, meanwhile, will count the number of lines

### Curl, Grep and Pipeline

+ `curl
  + Here's an example using two new commands `curl` and `sed` that will fetch Moby Dick from Project Gutenberg and count the number of occurences of “whale", case-insensitive:

```bash
$ curl -s http://www.gutenberg.org/cache/epub/2701/pg2701.txt | gunzip | sed  's/\s+/\n/g' | grep -i whale | wc -l
    1599
```

+ pipeline breakdown: `curl`
  + Here's a breakdown of each part of the pipeline and what it does:

```bash
curl -s http://www.gutenberg.org/cache/epub/2701/pg2701.txt
```

+ fetch Moby Dick from project Gutenberg and prints the results to stdout

+ pipeline breakdown: `sed`
  + `sed -r 's/\s+/\n/g'`
  + converts all whitespace(tabs, spaces, newlines) into newlines
  + This mean that each word gets its own line

+ pipeline breakdown: `grep`
  + `grep -i whale`
  + filters the output so that only lines that contain the word "whale" will be shown. `-i` makes the search case-insensitive

+ pipeline breakdown: `wc -l`
  + counts the number of lines from stdin and prints the result

### head and tail Commands

+ `head`
  + the `head` command prints the first part of a file
  + If a file isn't given, `head` reads from stdin
  + Read the first 3 lines of a file with `head -n3`

+ `tail`
  + the tail command prints the last part of a file
  + If a file isn't given, `tail` reads from stdin
  + Read the last 4 lines of a file with `tail -n4`
  + Read the last 9 bytes of a file with `tail -c9`

### Calendar and Dates

+ `cal`

```bash
$ cal
      九月 2019
日 一 二 三 四 五 六  
 1  2  3  4  5  6  7  
 8  9 10 11 12 13 14  
15 16 17 18 19 20 21  
22 23 24 25 26 27 28  
29 30
```

+ `date`

```bash
$  date
2019年 9月30日 星期一 10时24分01秒 CST
```

```bash
$ date +'%H:%M:%S'
10:25:33
```

```bash
$ date +'%F %T'
2019-09-30 10:26:50
```

### Shorten Lines

+ `fold`
  + sometimes it's handy to break long lines into shorter lines

### More Curl and Grep

+ `curl`
  + curl is a handy little tool for making HTTP requests

+ `grep`
  + You can search for patterns in files or from stdin with the `grep` command
  + The first argument is the pattern to search for as a regular experssion
  + Regular expression are a language for pattern matching

### Backticks and Arithmetic

+ backticks ``
  + Sometimes it's useful to include the output of a program in the arguments list of another
  + For Example with the date command we can print the current year
    + `date +%Y`
  + and we can use this value in a message with echo
    + echo Greetings from the year \`date +%Y\`
    + Greeting from the yare 2019
    + echo Greetings from the year $(date)
    + Greetings from the year 2019年 9月30日 星期一 10时47分20秒 CST

+ arithmetics
  + With `$((...))` expressions, you can do simple arithmetic on the command line
    + `echo $((4*5+1))`
    + 21

### Environment Variables

+ Environment variables
  + environment variables are defined by the shell and shell scripts
  + To list the current environment variables, type `export`

### Quotes

+ quotes
  + If you want to use characters like `<` or `>` in the arguments to a program, you will need to use quotes so that the shell doesn't try to interpret them
  + for example, to echo the string `<b>wow</b>` we can use single quotes
    + `echo '<b>wow</b>'`

## Bash Shell Scripts

### Introducing Scripts

+ Whenever you find yourself typing the same sequence of commands servel times, consider making a scripts!
+ Just put the commands you would normally type into a file and add `#!/bin/bash` to the top of the file

```sh
#!/bin/bash
mkdir wow
cd wow
echo "yay" > zing.txt
```

### Read, Loops and While

```sh
$ read MESSAGE
hello world

$ echo $MESSAGE
hello world
```

```sh
$ while true; do date; sleep 1; done
2019年10月 2日 星期三 06时45分55秒 CST
2019年10月 2日 星期三 06时45分56秒 CST
2019年10月 2日 星期三 06时45分57秒 CST
2019年10月 2日 星期三 06时45分58秒 CST
```

```sh
$ for x in {0..3}; do echo $x; done
0
1
2
3
```

```sh
while true; do node server.js; done;
```

### #PATH

+ `$PATH`
  + There is a special environment variable called `$PATH`

  ```sh
  $ echo $PATH
  /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
  ```

  + This variable contains a list of places separated by `:` that bash will look when you type a command

### Permissions

+ permissons
  + Each file on a UNIX system belongs to a user and a group
  + users are accounts on the system, like the one you logged with, groups are collections of users

+ To see what group you belong to, just type `groups`

```sh
$ groups
staff everyone localaccounts _appserverusr admin _appserveradm _lpadmin com.apple.sharepoint.group.1 _appstore _lpoperator _developer _analyticsusers com.apple.access_ftp com.apple.access_screensharing com.apple.access_ssh
```

+ To inspect the permissions on a file, use `ls -l`

```sh
$ ls -l
total 32
-rw-r--r--  1 liuyong  staff      0  9 29 10:49 beep.txt
-rw-r--r--  1 liuyong  staff      0  9 29 10:49 boop.txt
-rw-r--r--  1 liuyong  staff  12485 10  2 06:33 note.md
```

+ Here we can see that the file `beep.txt` is owned by the user `liuyong` and the group `staff`. There is also this part on the left:
  + `-rw-r--r--`
+ This string describes the permissions of the file

+ The first character is reserved for some fancy users, but after that there are 3 groups of 3 characters
  + `rwxrwxrwx`

+ Each character describes a permission: (**r**)ead, (**w**)rite, and e(**x**)ecute. A `-` in place of one of those letters means the permission is not available.
+ If the e(**x**)ecute bit is enable on a file for a user, it means the user can execute the file
+ If the e(**x**)ecute bit is enable on a directory for a user, it means the user can list the files in that directory
  + The first `rwx` says what the owner can do
  + The second `rwx` says what user in the group can do
  + The thied `rwx` says what everyone else can do
+ These three categories are called user(**u**), group(**g**), and other(**o**)

```sh
chmod 644
# add 4 for wirte
# add 2 for read
# add 1 for execute
# user can 4(write) + 2(read) = 6
# group can 4(write)
# other can 4(write)
```

+ `chmod`
  + To change the permission on a file, first figure out which capabilities you want to grant or revoke(rwx) from which categories of users(ugo)

+ To allow the owner of a file to  execute a script you can do
  + `chmod u+x script.sh`
+ which is the same as
  + `chmod +x script.sh`
+ beaucase the `u` is implied if not specified

+ You can also revoke permissions with a `-`. To make it so that other users can't write to a file:
  + `chmod o-w wow.txt`

+ You can grant and revoke permissions at the same time. Here we're adding read and execute permissions to the user while simultaneously revoking read and write from the group:
  + `chmod u+rxg-rw status.sh`
+ You can change the owner of a file with `chown` and the group with `chgrp`

### Exit Codes, Operators and Subshells

```sh
echo $?
# 上条命令是否执行成功，成功为0，失败不为0
```

```sh
$ test -f boop.txt && echo file exist
file exist

$ test -f abc.txt || echo "file dosen't exist"
file dosen't exist
```

```sh
$ if test -f abc.txt; then echo true; else echo false; fi
false
```

```sh
$ (if test -f abc.txt; then exit 1; else exit 2; fi); echo $?
2
```

### Job Control

+ Bash is built to handle multiple programs running in parallel

+ ctrl-c
  + Terminate a process in the foreground

+ ctrl-z
  + Put a process in the background

+ When you background a process with ctrl-z, the shell prints a message with `[N]`, `N` is the job id. Use `N` to refer a particular job or
  + `%%` - the most recent job

+ `&`
  + Another way to background a process is to use `&`

```sh
$ node &
[1] 15627
```

+ The job id of `node` is 1 and the process id is 15627, Job ids are local to a shell session, but process ids are global across the entire system

+ fg JOB
  + Move a process from the background to the foreground by its JOB

  ```sh
  jobs
  #当前后台任务
  fg %1
  #恢复任务号为1的任务
  vi cool.txt
  # ctrl z
  [1]  + 13439 suspended  vi cool.txt

  $ kill -9 %1
  [1]  + 13439 killed     vi cool.txt
  ```

+ `pgrep`
  + Search for a process by its name

+ `pkill`
  + Kill the process by its name

+ `kill ID`
  + Kill a process by its process or job id

### Screen

+ Install screen
  + `sudo apt-get install screen`

+ create a new named screen
  + `screen -S website`

+ list screen
  + `screen -list`

+ connect to a screen
  + `screen -x website`

+ detach from a screen
  + From inside of a screen, press CTRL + A then d.

+ create a new window inside screen
  + CTRL + A c

+ go to the next window
  + CTRL + A n

+ go to the previous window
  + CTRL + A p

+ close a window
  + Just type `exit` to close a window

## Regular Expressions

### Introducing Regular Expressions

[notes](https://github.com/substack/fmmn/blob/master/day1/regex.md)

+ regex is a pattern-matching language
+ many programming languages and system tools have a regex engine

+ System tools with regex
  + grep
  + sed
  + perl
  + vim
  + less

### Regular Expressions in JavaScript

+ Regex in JavaScript
  + str.split(re)
  + str.match(re)
  + str.replace(re, )
  + re.test(str)
  + re.exec(str)

+ replacing in JavaScript

```js
'1 two three\n'.replace(/1/, 'one')
// 'one two three\n'
```

+ similar to

```sh
$ echo '1 two three' | sed 's/1/one/'
one two three
```

+ match testing in JavaScript

```js
if(/^-(h|-help)$/.test(process.argv[2])) {
  console.log('usage:...')
}
```

+ capturing in JavaScript

```js
const m = /^hello (\S+)/.exec('hello cat')
console.log(m[1]) // cat
```

+ splitting in JavaScript

```js
'one_two-three'.split(/[_-]/)
// [ 'one', 'two', 'three' ]
```

### Flags, Metacharacters and Quantifiers

+ flags

```regex
/PATTERN/FLAGS
s/PATTERN/REP/FLAGS
```

+ `i` - case insensitive
+ `g` - match all occurences(global)
+ `m` - treat string as multiple lines
+ `s` - treat string as a single line

+ metacharacters
  + `.` matches any character
  + `?` zero or one time
  + `*` zero or more times
  + `+` one or more times
  + `[]` character class
  + `^` anchor at the beginning
  + `$` anchor to the end
  + `(a|b)` match a or b
  + `()` capture group
  + `(?:)` non capture group
  + `\d` digit `[0-9]`
  + `\w` word `[A-Za-z0-9_]`
  + `\s` whitespace `[ \t\n\r\f]`

+ `.` matches any character

```sh
$ echo hello beep boop | sed 's/b..p/XXXX/g'
hello XXXX XXXX
```

+ quantifiers
  + `?` - zero or one time
  + `*` - zero or more times
  + `+` - zero or more times

```js
'dog and doge'.replace(/doge?/g, 'DOGE')
// 'DOGE and DOGE'

'beep bp beeeep'.replace(/be*p/g, 'BEEP')
// 'BEEP BEEP BEEP'

'beep bp beeeep'.replace(/be+p/g, 'BEEP')
// 'BEEP bp BEEP'
```

### Character Class Ranges

+ `[...]`
  + Any characters inside the square brackets will match
  + For example, to match a vowel character: `[aeiou]`

```js
'beep and boop'.replace(/b[aeiou]+p/g, 'BXXP')
// 'BXXP and BXXP'
```

```js
let password = 'abc432!A'

const isLongerThan8 = (str) => /^[\w!@$%^&*\-+]{8,}$/.test(str)

const hasAnyLowercase = (str) => /[a-z]/.test(str)

const hasAnyUppercase = (str) => /[A-Z]/.test(str)

const hasNumber = (str) => /\d/.test(str)

const hasSymbols = (str) => /[!@$%^&*\-]/.test(str)

const isPasswordLegel = (pwd) => isLongerThan8(pwd) && hasAnyLowercase(pwd) && hasAnyUppercase(pwd) && hasNumber(pwd) && hasSymbols(pwd)

isPasswordLegel(password) // true

password = 'abc'

isPasswordLegel(password) // false
```

+ negated character class
  + `[^...]`
  + put a `^` after the opening square bracket in a character calss to negate it
  + for example, to match a non-vowel character: `[^aeiou]`

```js
'beep boop'.replace(/[^aeiou]/g, 'Z')
// 'ZeeZZZooZ'

'cool <beans> zzzz'.replace(/<[^>]+>/, 'XXX')
// 'cool XXX zzzz'

'cool "beans" zzzz'.replace(/"[^"]+"/, 'XXX')
// 'cool XXX zzzz'
```

+ character class sequences
  + Regex engines provide many pre-defined character class sequences
  + `\w` - work character: `[A-Za-z0-9_]`
  + `\W` - non-word character: `[^A-Za-z0-9_]`
  + `\s` - whitespace: `[ \t\r\n\f]`
  + `\d` - digit: `[0-9]`
  + `\D` - non-digit: `[^0-9]`

### Anchors, Groups and sed

+ anchors
  + `^` - anchor at the beginning
  + `$` - anchor to the end

+ groups
  + `(a|b)` - match a or b
  + `()` capture group
  + `(?:)` non capture group

```js
'beep boop'.replace(/b(oo|ee)p/g, 'XXX')
// 'XXX XXX'
'beep boop'.replace(/b(o|e){2}p/g, 'XXX')
// 'XXX XXX'
'cool <beans> zzz'.replace(/<([^>]+)> (\w+)/g, '"$1":$2')
// 'cool "beans":zzz'
'cat cat dog dog bird'.replace(/(\w+) \1/g, '$1')
// 'cat dog bird'
```

## Vim

### Introducing Vim

+ vim
  + vim is a popular command-line text editor

+ vim cheat sheet
  + Keep this handy as you experiment with vim
    + [http://www.fprintf.net/vimCheatSheet.html](http://www.fprintf.net/vimCheatSheet.html)
  + Here is another guide that covers the commands incrementally
    + [http://yannesposito.com/Scratch/en/blog/Learn-Vim-Progressively/](http://yannesposito.com/Scratch/en/blog/Learn-Vim-Progressively/)

+ Why vim?
  + edit files on a remote server over ssh
  + works without a graphical desktop environment
  + many programming-specific features
  + really fast editing

+ Remote editing
  + When you log in over ssh to administer a server, all you've got is a command-line interface
  + If you are comfortable with vim, you can work on the remote system with the same ease and familiarity as your local environment

+ Graphics not required
  + Not all systems have graphical environments!
  + What do you do if the graphical environment on your computer stops working?
  + What if you want to configure a device that doesn't have a graphics card?

+ Programming-specific features
  + Vim is very carefullly tuned to be effetive for programming
    + easily change the indentation on blocks of text
    + syntax highlighting for many programming languages
    + fluid interface with the system shell

+ Really fast editing
  + vim is designed from the ground-up to be very fast to use once you've learned its terse commands
  + Many of the commands are designed to keep your fingers on the home row of the keyboard so that you can drift seemlessly between editing and typing

+ alternatives
  + Here are some other command-line text editors
    + nano
    + emacs
    + vi
  + nano is much easier to learn than vim beaucase it doesn't have many features
  + emacs has a huge number of features and is very configurable
  + vi is a precursor to vim from 1983. vi's features are a subset of vim, and vi tends to already be installed on many systems

+ interface
  + Unlike many command-line programs, vim is interactive
  + vim uses ANSI codes to control a cuisor and position blocks of text on the screen

+ ansi codes
  + ANSI codes are special instructions that your terminal interprets and renders
  + ANSI codes can:
    + move the text cursor around
    + change colors
    + set modes

+ ansi codes - colors!
  + Try this command:

  ```sh
  echo -e '\x1b[38;5;44mwow'
  ```

  + also try changing "44" to some other values
  + Try stacking multiple color codes:
  
  ```sh
  echo -e '\x1b[38;5;44mso \ x1b[38;5;33mcool'
  ```

+ ansi codes
  + Applications like vim make heavy use of ansi codes

### Using Vim

+ and now: let's learn vim!
  + First: type `vim`
  + If that doesn't work, install vim
  + On a debian or ubuntu system do:
    + `sudo apt-get install vim vim-common`
  + to get vim plus extras like syntax highlighting

<!-- markdownlint-disable md033-->
+ now we're in vim!
  + Type <kbd>i</kbd> to go into insert mode
  + Now you can type normally

+ saving and quitting
  + Hit <kbd>esc</kbd> to get out of insert mode
  + Now type:
    + `:w foo.txt`
  + to save your file as `foo.txt`

+ You can go back into insert mode by typing <kbd>i</kbd> again or you can quit by typing
  + `:q`

+ Once you've quit, you can open your file back up again by running:
  + `vim foo.txt`

+ or you can do just `vim` and then from command mode do:
  + `:o foo.txt`

+ If you've opened a file already, you can just type `:w` to save the file, you don't need to type its name every time

### Modes and Moving Around

+ Modes
  + Time to recap what just happened
  + The first thing you'll notice is how we used 2 modes: command mode and insert mode
  + If you're in command mode, press <kbd>i</kbd> to go into insert mode
  + If you're in insert mode, press <kbd>esc</kbd> to go into command mode

+ If it says `-- INSERT --` at the bottom left of your terminal, you're in insert mode
+ Otherwise you're in command mode

+ vim is a language
  + Next, let's combine some commands
  + Try `:wq` to save and then quit
  + Try `:q!` to quit without saving

+ moving around - `hjkl`
  + In insert mode, the arrow keys do work, but you should practice not using them!
  + Instead, in command mode:
    + `h` - moves left one character
    + `j` - moves down one line
    + `k` - moves up one line
    + `l` - moves right one character

+ `hjkl` elsewhere
  + the `less` command uses `j` and `k` for up and down
  + [https://twitter.com](https://twitter.com) - `j` and `k`
  + many thing window managers such as xmonad

+ Moving around - even more!
  + you can move all kinds of places quickly in command mode:
    + `^` or `0` - move to the start of the current line
    + `$` - move to the end of the current line
    + `gg` jump to the beginning of the file
    + `G` - jump to the end of the file

### Deleting and Searching

+ delete
  + There are so many ways to delete!
    + `x` - delete the character under the cursor
    + `dd` - delete the current line
    + `d$` or `D` - delete from the cursor to the end of the current line
    + `d0` or `d^` - delete from the cursor to the start of the current line

+ You'll notice that we've already seen `0` and `$` before! You can repurpose each of the moving around commands to delete text
+ These all work:
  + `dG` - delete from the current position to the end of the file
  + `dgg` - delete from the current position to the start of the file
  + `dj` - delete the current line and the line below
  + `dk` - delete the current line and the line above
  + `2dd`, `3dd` etc - delete the next N lines

+ searching
  + You can search for text using regular expressions
    + `/PATTERN` - search for forward for PATTERN
    + `?PATTERN` - search for backword for PATTERN
  + Press:
    + `n` - jump to the next match
    + `N` - jump to the previous match

+ PATTERN is a regular expression, but you can just treat it as an ordinary text match for the most part
+ You can combine searching with deleting too:
  + `d/PATTERN` - delete to the next match of PATTERN
  + `d?PATTERN` - delete to the previous match of PATTERN
  + `dn` - delete to the next already matched pattern
  + `dN` - delete to the previous already matched pattern

+ jumping
  + You can also skip ahead to individual characters in a simple way on the current line"
    + f + CHAR - search forward on the current line to CHAR
    + t + CHAR - search forward on the current line to the character before CHAR
    + F + CHAR - search backward on the current line to CHAR
    + T + CHAR - search backward on the current line to the character after CHAR

+ These are very useful in combination with the delete operators! They combine as you might expect:
  + df + CHAR - delete forward on the current line to CHAR
  + dt + CHAR - delete forward on the current line to the character before CHAR
  + dF + CHAR - delete backward on the current line to CHAR
  + dT + CHAR - delete backward on the current line to the character after CHAR

### Search and Replace

+ search and replace
  + `:s/PATTERN/REPLACEMENT/FLAGS/`
+ Try these on a line with the string cats:
  + `:s/cat/dog/`
  + `:s/cat/dog/g`
  + `:s/cat/dog/i`

+ replace everything
  + `:%s/cat/dog/ig`
  + Replaces "cat" with dog everywhere in the entire file, case insensitively

+ regex flags
  + `i` - case insensitive
  + `g` - global repalce(per line)

### Visual Select

+ visual select
  + Press <kbd>v</kbd> to go into visual select mode
  + Move the cursor around to select text
  + Once you've selected a block, you can press:
    + `y` - "yank" the text into the paste buffer
    + `x` or `d` - delete the selected text
    + `>>` - indent the text right by shiftwidth
    + `<<` - indent the text left by shiftwidth

+ visual modes
  + `v` - select by characters
  + `V` - select by lines
  + ctrl + v - select in a block

### Paste Buffer and Insert Modes

+ Once you've populated the paste buffer by yanking or deleting, press <kbd>p</kbd> to paste

+ More insert modes
  + There are more ways to insert mode than just `i`:
  + `o` - go into insert mode, inserting a new line below the current line
  + `O` - go into insert mode, inserting a new line above the current line
  + `a` - go into insert mode at one character to the right
  + `A` - go into insert mode at the end of the current line

+ fancy odds and ends
  + `J` - move the next line to the end of the current line
  + \` + . - jump to the last edit

+ insert a file
  + You can insert a file at the cursor position with:
    + `:r otherfile.txt`

+ insert with a command in place
  + You can insert the output of a command at the cursor position with `:r!`
  + For example, to insert the output of the `pwd` command:
    + `:r!pwd`

+ `.vimrc`
  + autoindento
  + expandtab
  + tabstop
  + shiftwidth(sw)

+ `set -o vi`
  + You can use vi shorthand in bash too
  + just do:
    + `set -o vi`
  + now press esc and hjkl your way around

+ escape is too far away
  + It's common for vim users to remap their keyboards
  + One common thing to do is swap the caps lock key with the escape key because escape is such a common key in vim

+ xmodmap for escape
  + In linux you can use xmodmap to remap your keys
  + Save this text to a file called `.xmodmap` in your home directory:

  ```sh
  remove Lock = Caps_Lock
  keysym Escape = Caps_Lock
  keysym Caps_Lock = Escape
  add Lock = Caps_Lock
  ```

  + now run `xmodmap ~/.xmodmap` to enable your swapped keys
  + Add this command to your login scripts so that each time you log in you won't need to remember to run the command every time you log in

+ built-in escape alternative
  + you can alse use ctrl + \] to get out of insert mode
