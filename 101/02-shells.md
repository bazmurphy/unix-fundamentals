# Shells

## What is a shell?

A [Li\|U]nix shell is the command-line interface between the user and
the system. It is used to perform some action, specifically, typing
commands and displaying output, requested by the user.

## Introduction to Bash

**Bash** is known as the Bourne-again shell, written by Brian Fox, and
is a play on the name of the previously named Bourne shell (/bin/sh)
written by Steve Bourne.

## Shell Fundamentals

### Command-line Editing Modes

Over time, as one progresses with using the shell, it will become
necessary to execute commands that may contain many arguments or have
output piped to other commands. Beyond simple commands such as `cat` and
`ls`, it can be tedious should one need to re-type, or, in most cases,
edit, a given command. Luckily, `bash` provides a facility to make this
scenario easier: the command-line editing mode.

The command-line editing mode emulates the movement functions of two
common text editors, `emacs` and `vi`. In the case of the shell, the
cursor's movement is being controlled.

By default, `bash` operates in `emacs` mode.

#### Example Edit Commands

The following commands are very common while using the `emacs` mode.

- `Ctrl-b`: Move backward by one character
- `Ctrl-f`: Move forward by one character
- `Ctrl-a`: Move to the beginning of the line
- `Ctrl-e`: Move to the end of the line
- `Ctrl-k`: Delete from the cursor forward
- `Ctrl-u`: Delete from the cursor backward
- `Ctrl-r`: Search the command history

The following commands are very common while using the `vi` mode.

- `h`: Move backward by one character
- `l`: Move forward by one character
- `w`: Move forward by one word
- `0`: Move to the beginning of the line
- `$`: Move to the end of the line
- `d$`: Delete from the cursor to the end of the line
- `d0`: Delete from the cursor the beginning of the line
- `:history s`: Search the command history

#### Setting the Mode

One can manually set the desired mode using the below commands.

- `emacs`

  `set -o emacs`

- `vi`

  `set -o vi`

### Environment variables

Environment variables are used to define values for often-used
attributes of a user's shell. In total, these variables define the
user's environment. Some environment variables provide a simple value
describing some basic attribute, such the user's current directory
(`$PWD`). Others define the behavior of a command, such as whether or
not the `history` command should log repeated commands individually or
log the repeated command once (`$HISTCONTROL`).

#### $PATH

The most common, or most recognized, environment variable is the `$PATH`
variable. It defines the set of directories that the shell can search to
find a command. Without an explicit path provided when calling a command
(i.e. `/bin/ps`), the shell will search the directories listed in the
`$PATH` variable until it finds the command. If the command is not found
in any of the defined directories in `$PATH`, the shell will produce an
error explaining as much. :

```sh
$ foobar -V
-bash: foobar: command not found
```

To view the contents of the `$PATH` variable, use `echo` to print the
variable's value:

```sh
$ echo $PATH
/usr/local/sbin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/bin
```

The order of the directories in the `$PATH` variable, from left to
right, is important; when searching directories for a command, the shell
will stop looking after it finds its first match. In other words, using
our example `$PATH` variable above, if there is a version of `ps` that
exists in `/usr/local/bin` that is preferred (by the sysadmin) over the
version that exists in `/bin`, the shell will still execute `/bin/ps`
due to the precedence of the directories defined in the `$PATH`
variable.

To list all of the shell's environment variables, use the `env` command:
:

```sh
$ env
HOSTNAME=foobar
SHELL=/bin/bash
TERM=xterm
HISTSIZE=1000
USER=root
PATH=/usr/local/sbin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin:/usr/local/bin
MAIL=/var/spool/mail/root
PWD=/root/curriculum
PS1=[\[\e[33;1m\]\t \[\e[31;1m\]\u\[\e[0m\]@\[\e[31;1m\]\h\[\e[0m\] \W\[\e[0m\]]#
HISTCONTROL=ignoredups
SHLVL=1
SUDO_COMMAND=/bin/bash
HOME=/root
HISTTIMEFORMAT=[%Y-%m-%d %H:%M:%S]
OLDPWD=/tmp
```

### Shell Profiles

Shell profiles are used to define a user's environment. In cases where
an environment variable needs to be set or a script needs to be run at
login, a profile can be used to ensure this happens consistently and
automatically. Anything that can be run in a standalone shell script can
be placed in a profile.

There are two types of profiles:

- Global profile (`/etc/profile`)
- User profile (`~/.bash_profile`)

#### `/etc/profile`

This is the global profile. Any environment variable set in this file
applies to all users. Any script called from this file is executed for
all users.

#### `~/.bash_profile`

This is the user profile. Any environment variable set in this file
applies to the user only. Any script called from this file is executed
for the user only.

##### Profile Precedence

**NOTE**: It is possible to override settings from `/etc/profile` via
`~/.bash_profile` as `~/.bash_profile` is executed after `/etc/profile`.

### Special Environment Variables

Certain variables exist that provide useful information about what is
happening in the environment. For example, it may be necessary to know
the ID of a running process or the exit status of an executed command.

#### Process ID: `$$`

To determine the process ID (PID) of the current shell, first run `ps`
to find the PID, then run `echo $$` to confirm the PID. :

```sh
$ ps -efl | grep bash
  502 20440 20439   0 10:25PM ttys001    0:00.01 -bash                4006  31  0  2433436   1228 -      S                   0
  502 20447 20440   0 10:29PM ttys001    0:00.00 grep bash            4006  31  0  2432768    620 -      R+                  0
$ echo $$
20440
```

```sh
baz@baz-pc:/etc/profile.d$ ps -efl | grep bash
0 S baz        41367   41342  0  80   0 -  3764 do_wai 20:07 pts/1    00:00:00 bash
0 S baz        43329   41367  0  80   0 -  2280 pipe_r 20:13 pts/1    00:00:00 grep --color=auto bash
```

#### Background Process ID: `$!`

Occasionally, commands will need to be executed in the background (via
the `&` operator). The PID of that process can be found using the `$!`
variable. For example, call `sleep` for 30 seconds and place it in the
background. Then `echo $!` to see the PID. Alternatively, call `ps` to
confirm the PID. :

```sh
$ sleep 30 &
[1] 20450
$ echo $!
20450
$ ps -efl | grep sleep
502 20450 20440   0 10:33PM ttys001    0:00.00 sleep 30             4006  31  0  2432748    496 -      S                   0
```

```sh
baz@baz-pc:/$ sleep 30 &
[1] 43811
baz@baz-pc:/$ echo $!
43811
baz@baz-pc:/$ ps -efl | grep sleep
0 S baz        43811   43541  0  80   0 -  2064 hrtime 20:14 pts/1    00:00:00 sleep 30
0 S baz        43906   43541  0  80   0 -  2281 pipe_r 20:14 pts/1    00:00:00 grep --color=auto sleep
```

#### Exit Status: `$?`

When a command is executed, it always has an exit status. That status
defines whether or not the command was successful. For success, the exit
status is **0**. Non-zero values denote failure. Many commands provide
multiple exit codes to help define what the reason for failure was. This
helps the user troubleshoot any problems.

As an example, try to list a known file within a user's home directory,
then list a file that is known **not** to exist. After each command,
execute `echo $?` to see the exit status. :

```sh
$ ls .bash_profile
.bash_profile
$ echo $?
0
$ ls foobar
ls: foobar: No such file or directory
$ echo $?
1
```

```sh
baz@baz-pc:~$ ls .profile
.profile
baz@baz-pc:~$ echo $?
0
baz@baz-pc:~$ ls foobar
ls: cannot access 'foobar': No such file or directory
baz@baz-pc:~$ echo $?
2
baz@baz-pc:~$
```

### History

The history is a handy facility within `bash`; it stores all of the
commands that the user has executed.

To see the history, simply type `history` and all of the stored commands
will be displayed to the terminal. Similarily, run `cat ~/.bash_history`
to see all stored commands.

#### Re-executing Commands

A benefit of storing command history is that the commands can be easily
recalled. To execute the last command, type `!!`:

```sh
$ ls
file1 file2 dir1
$ !!
ls
file1 file2 dir1
```

Note that the last command is echoed just above the output of that
command.

To execute the nth command in history, run `!n` where `n` is the line
number of the command as found in the output of `history`:

```sh
$ history | less
  1  ls -la
  2  ls -F
  3  pwd
$ !2
ls -F
file1 file2 dir1/
```

##### Searching History

Finally, one can search the history by typing `Ctrl-r` followed by a
string to match a command:

```sh
$ (reverse-i-search)`ls': ls -F
```

To execute the command (if a match is found), simply type Enter.

### Job control

From time to time, it may be necessary to manage commands running in the
background. These are typically referred to as jobs. A command can be
placed in the background via the `&` operator. Use the `jobs` command to
see the job in the background. To bring it back to the foreground, run
`fg`:

```sh
[23:24:22 ~]$ sleep 30 &
[1] 20969
[23:24:26 ~]$ jobs
[1]+  Running                 sleep 30 &
[23:24:29 ~]$ fg
sleep 30
[23:24:56 ~]$
```

```sh
baz@baz-pc:~$ sleep 5 &
[1] 55127
baz@baz-pc:~$ fg
sleep 5
baz@baz-pc:~$
```

While in the foreground, the job can be suspended via `Ctrl-z` and sent
to the background once more using `bg`:

```sh
[23:24:56  ~]$ sleep 30 &
[1] 21078
[23:28:25  ~]$ jobs
[1]+  Running                 sleep 30 &
[23:28:27  ~]$ fg
sleep 30
^Z
[1]+  Stopped                 sleep 30
[23:28:33  ~]$ bg
[1]+ sleep 30 &
[23:28:37  ~]$ jobs
[1]+  Running                 sleep 30 &
[23:29:39 ~ ]$ jobs
[1]+  Done                    sleep 30
```

#### Multiple Jobs

It is possible to have multiple jobs running in the background at the
same time. Use the `jobs` command to track them via their job ID, noted
between the square brackets after sending a job to the background.
Knowing the job ID is helpful in the event that the job needs to be
pulled into the foreground (via `fg`) or if the job needs to be killed:

```sh
[23:36:01  ~]$ sleep 120 &
[1] 21086
[23:36:16  ~]$ sleep 240 &
[2] 21087
[23:36:20  ~]$ jobs
[1]-  Running                 sleep 120 &
[2]+  Running                 sleep 240 &
[23:36:21  ~]$ fg %1
sleep 120
^C
[23:36:33  ~]$ jobs
[2]+  Running                 sleep 240 &
[23:36:35  ~]$ kill %2
[23:36:39  ~]$ jobs
[2]+  Terminated: 15          sleep 240
```

```sh
baz@baz-pc:~$ sleep 60 &
[1] 57009
baz@baz-pc:~$ sleep 30 &
[2] 57019
baz@baz-pc:~$ jobs
[1]-  Running                 sleep 60 &
[2]+  Running                 sleep 30 &
baz@baz-pc:~$ fg %1
sleep 60
^C
baz@baz-pc:~$ jobs
[2]+  Running                 sleep 30 &
baz@baz-pc:~$ kill %2
baz@baz-pc:~$ jobs
[2]+  Terminated              sleep 30
baz@baz-pc:~$
```

**NOTE**: When manipulating jobs with any command, the jobs are
described by their ID using the `%n` notation where `n` is the job ID.

For information on ensuring running jobs continue, even when terminal
connectivity is lost, see the sections on `gnu-screen` and `tmux`.
