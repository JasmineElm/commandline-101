# commandline-101

Some basic commandline commands to get you started 🚀

You can do a lot with the commandline, It's a great way to quickly, and
_replicably_, perform tasks.

## update and upgrade

First job in a new install is to update and upgrade the system. This will ensure
you have the latest software and security patches. We suggest Raspbian which is
[debian based](https://wiki.debian.org/).

- **Update the package list** `sudo apt update`
- **Upgrade the system** `sudo apt upgrade`
- **Upgrade the system** `sudo apt full-upgrade` (You are unlikely to need this)
- **Remove unneeded packages** `sudo apt autoremove --purge`

## Who, What, Where

- **Who am I?** `whoami`
- **Where am I?** `pwd` (print working directory)
- **What's in this directory?** `ls` (list)
- **what is the name of this machine?**`hostname`
- **what operating system is this machine running?** `uname`

## Navigation

Navigation is _relative_; paths are relative to the current directory, unless
you select an absolute path.

The root directory is `/` Everything branches from here. The home directory is
`~` or `/home/username`. The current directory is `.` and the parent directory
is `..`. You can use `~` to refer to the home directory, and `~username` to
refer to a different user's home directory. A typical structure is:

```txt
/
├── bin (binaries)
├── boot (boot loader)
├── dev (devices)
├── etc (configuration)
├── home (home directories)
├── lib (libraries)
├── media (removable media)
├── mnt (mount points)
├── opt (optional software)
├── proc (process information)
├── root (root user home)
├── run (run-time data)
├── sbin (system binaries)
├── srv (service data)
├── sys (system information)
├── tmp (temporary files)
├── usr (user programs)
└── var (variable data)
```

When you install software, it will typically go in `/usr/bin` or
`/usr/local/bin` for system-wide installations, or `~/bin` for user
installations. Configuration files are typically in `/etc` or `~/.config`.
Temporary files are in `/tmp` or `/var/tmp`. Logs are in `/var/log`. You can see
the structure of the filesystem by running `ls /`, or installing `tree` and
running `tree -L 1 /`.

- **Change directory** `cd` (change Directory)
- **go to the home directory** `cd ~`, or more simply `cd`
- **go to the previous directory** `cd -`
- **Move up one directory** `cd ..`
- **Move up two directories** `cd ../..`

## printing to the screen

There are two streams of output in linux, `stdout` and `stderr`. `stdout` is
where the output of a command goes, `stderr` is where the errors go. You'll come
across `stdout` a lot, but `stderr` is important too.

- **Print to the screen** `echo "Hello, World!"`
- **Print to the screen** `printf "Hello, World!\n"` (more control over
  formatting)

To redirect stderr to stdout, use `2>&1` after the command; this is very useful
in scripting.

You can also format output using `tput`, to make text bold, underlined and so
on. This will only work in more modern terminals which support ANSI escape
sequences.

- **Make text bold** `tput bold`
- **Make text underlined** `tput smul`
- **Make text red** `tput setaf 1`
- **Reset text** `tput sgr0`
- **Reset text** `tput reset`

example: `echo "$(tput bold)Hello, World!$(tput sgr0)"`

## Files

Everything is a _file_ in linux, this means you can read and write them. You can
find out a lot about a file using the `ls` command.

- **Create a file** `touch filename` OR `> filename`
- **Create a directory** `mkdir dirname`
- **Remove a file** `rm filename`
- **Remove a directory** `rm -r dirname`
- **Copy a file** `cp file1 file2`
- **Move a file** `mv file1 file2`
- **View a file** `cat filename` often better to use a pager like `less` or
  `more`
- append to a file `echo "text" >> filename`
- replace the contents of a file `echo "text" > filename`

## Searching

Searching can be done using `ls`, `find`, `grep` and `locate`.

`ls` accepts _globs_; these are wildcards which allow you to search for files.

- **Find files with a .txt extension in the current directory** `ls *.txt`
- **Find files with a .txt extension in the current directory and
  subdirectories** `ls -R *.txt`, or `ls **/*.txt` (if you have `globstar` set,
  you probably do)

ls is handy for quick one liners, but can be _brittle_. For more complex
searches use `find`. Find is more complex, and can take a little practice to use
properly. A simple use-case is to find all files in the current directory.

The syntax is `find <path> <options> <expression>`. The path is the directory

`find . -type f` will find all files in the current directory.  
`find . -type d` will find all directories in the current directory.
`find . -name "*.txt"` will find all files with a .txt extension in the current,
this is **case sensitive**; `example.TXT` will not be found. To make it case
insensitive use `-iname` instead of `-name`.

A common scenario is that you know the contenst of a file, but not the name. For
this we can use `grep` (Global Regular Expression Print). This is a very
powerful tool, particularly if you know
[regular expressions](https://regexr.com/).

Example: we'll search for the word "hello" in all files in the current
directory, after creating a file with that word in it.

```bash
echo "hello" > file.txt
# grep <flag> <pattern> <file/path>
grep -r "hello" .
```

`locate` is a tool which uses a database to quickly find files.  
You may not have locate installed, but you can install it using your package
manager: `sudo apt install mlocate` The database is populated by the
`sudo updatedb` command. You can then search for files using `locate <pattern>`.
This is very fast, but the database is only updated via a cron job, so it may
not be up to date if you have just created a file.

## what am I looking at?

once you've found the file you're interested in, you can use either `file` to
get some basic information, or `stat` to get more detailed information.

- **Get basic information about a file** `file filename`
- **Get detailed information about a file** `stat filename`

If the _file_ is a command, you can use `type` to find out what it is. This
works for aliases, functions, and builtins too.

## Permissions

Permissions are very important in linux, they control who can read, write and
execute files. Permissions are shown in the output of `ls -l` as a series of 10
characters. The first character is the file type, the next 9 are the
permissions. The permissions are in three groups of three, for the

1. owner
2. group
3. others

The permissions are `r` for read, `w` for write, and `x` for execute. These are
represented as `rwxrwxrwx` or a a number `777`. The numbering is based on the
binary representation of the permissions, so `rwx` is `111` in binary, which is
`7` in decimal.

Read is `4`, write is `2`, and execute is `1`. You can add these numbers
together to work out the permissions. For example, `rwx` is `4+2+1=7`, `rw-` is
`4+2=6`, and `r-x` is `4+1=5`. You can set permissions using `chmod`. For
instance:

```bash
touch example # creates an empty file
ls -l example # shows the permissions - these are set by a mask defined in /etc/profile
chmod 644 example # sets the permissions to read and write for the owner, and read for the group and others
ls -l example # shows the new permissions
# perhaps easier, we can use the explicit `ugo` (user, group, owner) syntax:
chmod u=rwx,g=rwx,o=rwx example # sets the permissions to read, write and execute for everyone
ls -l example # shows the new permissions
```

A common scenario is that you have just created a script and want to make it
executable. For this, we can use `chmod +x script.sh`. This will add the
executable flag to the script. You can also use `chmod -x script.sh` to remove
this.

```bash
echo "echo 'Hello, World!'" > script.sh
chmod +x script.sh
./script.sh
```

## chaining commands

Linux is based un Unix which has the philosophy of "do one thing, and do it
well". This means we have a lot of small tools which can be chained together to
perform complex tasks. This is done using pipes `|` and redirection `>`. Pipes
redirect the output of one command to the input of another, and redirection
sends the output of a command to a "file".

## variables and aliases

Variables are a core part of coding, and bash is no exception. You can set a
variable using `var=value` (**note: no spaces around the `=`**), and then use it
with `$var`. You can also use `${var}` to make it clear where the variable name
ends.

Your profile will have some variables set, such as `PATH`, `EDITOR`, and `PS1`.
you can see these by calling `env` or `printenv`. You can set your own variables

## Bashisms

Bash is packed with features to make life easier.

- **Command substitution** `$(command)` This runs the command in a subshell and
  returns the output. This is very useful for setting variables, or passing
  arguments to a command.
- **Variable assignment** `var=value` This sets a variable. You can then use
  this variable in your script. Variables are case sensitive.
- **Variable expansion** `$var` This expands the variable. You can also use
  `${var}` to make it clear where the variable name ends.
- **Arithmetic expansion** `$((1+1))` This expands the arithmetic expression.
- **expansion** `{1..10}` This expands to `1 2 3 4 5 6 7 8 9 10`. You can also
  use `{a..c}` t

```bash
echo "it is now $(date)" #this will run the date command and print the output
for i in {1..10}; do #the number is assigned to variable $i
  echo "$i plus 1 is $((i+1))" #the arithmetic expansion is used to add 1 to $i
done
```

There are conditional statements in bash, which allow you to run commands based
whether the condition is true or false. This is a bit shorter than writing full
`if` statements.

- **if the previous command was successful** `command && echo "success"`
- **if the previous command failed** `command || echo "failure"`

You can also use `if` statements in bash, which are a bit more flexible, and
easier to read.

```bash
if [ -f file.txt ]; then
  echo "file.txt exists"
else
  echo "file.txt does not exist"
fi
```

or even case statements, which are useful for more complex scenarios.

```bash
case "$1" in
  start)
    echo "Starting service"
    ;;
  stop)
    echo "Stopping service"
    ;;
  restart)
    echo "Restarting service"
    ;;
  *)
    echo "Usage: $0 {start|stop|restart}"
    exit 1
    ;;
esac
```

## Editing files

The course materials suggest using `nano` to edit files. This is a simple modal
editor, which is easy to use. Modern editors such as `vscode` offer a fuller
experience, but they are not available on the commandline. If you want to use
the commandline exclusively (you should!), then you might want to consider using
`vim`. This has a much steeper learning curve, but once you get the basics, it
is very powerful. Your system will have a default editor set in the `EDITOR`,
try `echo $EDITOR` (or `env | grep EDITOR`). Having an editor set means you can
can edit files using `edit filename`. It also means that other programs which
need to open an editor will use this one.

### exiting vim

Yes it's a meme, but it does
[crop up on learners forums](https://www.reddit.com/r/linux4noobs/comments/p5i418/how_do_i_quit_vim/).
To exit vim, you can use `escape` to enter command mode, then `:` to enter
command mode, then `q` to quit. Vim will not let you quit if you have unsaved
changes, so you may need to use `:q!` to force quit without saving. You can also
use `:wq` to write and quit, or `:w` to write the file.

## Writing your first script

The classic script is the "Hello, World!" script. This is a simple script that
prints "Hello, World!" to the screen. You can write this in any language, but
we'll use bash.

The first line of the script is the shebang `#!`, followed by the path to the
interpreter. This tells the system which interpreter to use to run the script.
For bash, this is `#!/bin/bash`. You can also use `#!/usr/bin/env bash` to use
the bash interpreter in the user's path (recommended!). This is useful if you
are writing a script which will be run on multiple systems, as the path to the
interpreter may be different.

nano hello.sh # create a new file called hello.sh

```bash
#!/usr/bin/env bash
echo "Hello, World!"
```

two lines of code, and you have a script! At the moment, although you have the
contents of the script, it won't run. This is because the script is not
executable. There's two options here, you can call the script with an explicit
interpreter:

```bash
bash hello.sh
```

This will run the script using the bash interpreter. It's fine to do this if you
are always going to be in front of the computer when script is called, but it's
not very user friendly. The better option is to make the script executable, we
can do this quickly using `chmod +x hello.sh`. This will add the executable flag
to the script. You can also use `chmod -x hello.sh` to remove this.

```bash
chmod +x hello.sh
./hello.sh
```

Success! You have written and run your first script. The best way to learn is by

## How do I...?

A common scenario is that you know what you want to do, but not how to do it.
This can be that you know the name of the command, but not the arguments, or
that you don't know which command to use.

### I don't know what I'm looking for

you can use `apropos` to search for installed commands. For instance, say you
want to compress a file, but you don't know the command. You can use
`apropos compress` to identify the command. This will list all commands which
have the word "compress" in the description.

### I know what I'm looking for, but not the arguments

You can use `man` to read the manual for a command. This will give you a (very)
comprehensive guide to the command, including the arguments, options, and usage.
Most commands also have a `--help` flag, which will give you a brief overview of
the command, and the options. If you find the outputs of both too confusing or
verbose, there are also `tldr` pages, which are community maintained, and give
common use-cases.

```bash
apropos compress
# gzip should appear in the results
man gzip
# this 309 line manual will give you all the information you need, but perhaps you just want a quick overview
gunzip --help
# this will give you a brief overview of the command
```

[visit the tldr pages](https://tldr.inbrowser.app/pages/common/gzip)

## How did I do that?

You can use `history` to see the commands you have run. This is a good way to
see what you have done, and to repeat commands. You can also use `!` to repeat a
given command. History records the individual commands, so if you

```bash
mkdir example
cd example
echo "Hello, World!" > file.txt
```

you should see each of these commands in the history, for example:

```txt
  270  15-04-24 23:43  mkdir example
  271  15-04-24 23:43  cd example
  272  15-04-24 23:43  echo "Hello, World!" > file.txt
```

if you want to repeat the `echo` command, you can use `!272`. You can also use
`!-1` to repeat the last command, `!!` to repeat the last command, or `!echo` to
run the last command which started with `echo`.

You can also filter the history using `history | grep command` to find specific
text, e.g., `history | grep echo` will show something like this:

```txt
    272  15-04-24 23:43  echo "Hello, World!" > file.txt
```

## What is the computer doing?

You can see what the computer is doing using `top` or `htop`. These give a good
visual representation of the system, including the CPU and memory usage. If you
have a runaway process, it is easy to spot and kill. On a busy system, you may
find it difficult to see the wood for the trees, in which case you can use `ps`
A good use-case for `ps` is to find the PID of a process, and then kill it using
`kill`

```bash
ps -ef | grep processname
# identify the pid of the process
kill -9 pid
```

Both `top` and `ps` query information in `/proc`, which is a pseudo-filesystem
(remember: everything is a file). This is a virtual filesystem, which is used to
track information about the system. You can see the information in
`/proc/cpuinfo`, `/proc/meminfo`, and `/proc/version`. This is a good way to see
the information about the system, without needing to install additional tools.
