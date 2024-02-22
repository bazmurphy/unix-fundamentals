# Useful shell tools

## The UNIX Philosophy

- Unix characterised by modular design philosophy
- Unix programs are small, single purpose tools that can be easily chained together with other Unix programs
- Modular approach allows for simpler, flexible system, you can combine multiple command line programs to solve a unique problem without having to write a new program to get the job done

## Working with your system

### `ps`

- `ps` shows processes currently running on the system
- `ps` takes many arguments but some good recipes are:
- `ps aux` to see all processes from a user standpoint (whether they have tty or not)
- `ps -lfU <username>` to see all processes owned by `<username>` in long format
- `ps` has outformatting with the `-o` switch and works well with `grep`.
- `pgrep` combines `ps` and `grep` into one command

### `top`

- `top` shows the top most cpu intensive processes in a table form with system stats summarized at the top
- it refreshes every 1 second
- press `m` while running `top` will not sort by processor use but my memory use

### `df`

- `df` looks at all your mounted filesystems and reports on their status
- filesystem type, total size, available space, percent used, and mountpoint
- `df -h` will show the sizes in human readable form
- `df -h .` will show only the filesystem the current working directory is on, very useful when debugging `nfs` and `autonfs` mounts

```sh
baz@baz-pc:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           784M  2.2M  782M   1% /run
/dev/nvme0n1p5  251G   65G  174G  28% /
tmpfs           3.9G   13M  3.9G   1% /dev/shm
tmpfs           5.0M   16K  5.0M   1% /run/lock
efivarfs        128K   26K   98K  22% /sys/firmware/efi/efivars
/dev/nvme0n1p1   96M   49M   48M  51% /boot/efi
tmpfs           3.9G     0  3.9G   0% /run/qemu
tmpfs           784M  128K  784M   1% /run/user/1000
/dev/sda2       1.9T  1.7T  184G  91% /media/baz/8xxxxxxxxxxxxxxF
```

### `du`

- `du` estimates the size on disk of a file or files
- `du -h` returns the usage information in human readable format
- if the argument to `du` is a `directory` `du` will run and return a new value for each file in that directory, recursing into subdirectories as well
- `du -sh` can be run on a directory to prevent this behaviour and return the sum of the space on disk of all files under that directory

### `find`

- `find` is for finding files on the system
- `find .` recursively walks the entire filesystem tree below the current working directory and prints out each file bny its relative path
- `find . -name 'test'` does the same as above but only prints out files name test
- `find . -name 'test' -type d` prints out only directories
- `find . -name 'test' -type f` prints out only regular files
- `finame . -name '*.html'` -name accepts wildcards, will only print out .html files

### `kill`

- `kill` is used to send a signal to a process - interrupting it
- the most common use of this is to stop or kill a process

- to stop a process with PID 123
- `kill 123`

- once the signal is sent the process can decide what to do with it
- the above `kill 123` sends a `TERM` signal to the process with `PID 123`
- `TERM` or `SIGTERM` means "Software Termination Signal"
- some processes will notice that signal and finish what they are doing and shut down
- others will just shut down immediately
- others will ignore it entirely

- there are many more types of signals `man signal` for a list

- if the `kill 123` didn't work you can use
- `kill -KILL 123` sending a `KILL` signal to the process

- every signal has a name and a number. you can reference them by either one. another way of running `KILL`
- `kill -9 123`

- be careful when using `KILL` signal, it cannot be caught by the process

- the process cannot gracefully shut down, and can lead to temporary files not being removed, open files not being closed, or even corruption of database files

- Signals can be used in a wide variety of ways, not just for terminating processes.
- If your system is running Unicorn processes you can send a `TTIN` signal to the master process and it will spawn an additional work
- You can send a `TTOU` signal and it will remove one of the workers.
- Apache HTTPD Web Server accepts `USR1` which causes it to close and re-open log files, useful when rotating your log file
- More on signals: https://www.opsschool.org/unix_signals.html

### `ls`

- `ls` is used to list the contents of a directory. It's most basic usage would be to list the contents of your shell's current working directory
- `ls /usr/local` list the contents of a specific directory
- files and directories that bein with `.` are "hidden files"
- `ls -a` will list hidden files/directories
- `ls -l` outputs long listing, where various attributes are given in addition to the filenames

- `ls -la` use both together

- in long listing:

  - [1] file type, permissions any special attributes
  - [1a] file type `d` for directory and `-` regular file
  - [1b] attributes
  - [1c] permissions
  - [2] links
  - [3] owner
  - [4] group
  - [5] file size
  - [6] modification date
  - [7] file name

- also listed at the top are:
  - `.` current directory
  - `..` one level up

### `lsof`

- `lsof` lists open files
- useful to examine what a particular process or user happens to be doing on a system
- for each open file information is listed

  - process id that holds the file open
  - the command that started the process
  - the name of the user running the process

- doesn't just list regular files, can be used to examine network activity with `lsof -i`

### `man`

- `man` command is used to access `man` pages
- manual pages are documentation on a particular aspect of the operating system: commands, config files, functions from a library etc.
- `man <name of command>`
- there are `man` pages for most programs on your system
- `man bash` at the top is `BASH(1)` in section 1
- so may have to include section number when running `man`

- `man` page sections:

  - section 1: general commands
  - section 2: system calls (functions provided by the kernel)
  - section 3: library functions (functions within program libraries)
  - section 4: special files (usually found in /dev)
  - section 5: file formats and conventions (eg /etc/passwd)
  - section 6: games and screensavers
  - section 7: miscellaneous
  - section 8: system administration commands

- to search through the `man` pages

  - `man -k` or `apropos <search term>`
  - returns a list of man pages which match

- `info` command find out info about the system and its utilities

### `mount`

- the `mount` command is used to mount filesystems
- when no arguments, lists the filesystems currently mounted
- to mount an ext4 filesystem that resides on `/dev/sda2` partition

  - `mount -t ext4 /dev/sda2 /mnt`

- `mount` consults `/etc/fstab` and uses the entries/options there if possible

  - if an entry for `/home` exists in `/etc/fstab` you can issue the command there
  - it would mount whatever partition is found that is associated with `/home` entry in `/etc/fstab` and use any options present

- items don't necessarily have to be a partition on a disk to be mounted (.iso etc)

  - `mount -o loop -t iso9660 /home/luser/installer.iso /mnt/cdrom`

- `mount` can also operate on currently mounted filesystems

  - switching a currently mounted filesystem from read-write to read-only so that a filesystem check can be performed
  - `mount -o remount, ro /` remount the currently mounted `/` filesystem as read only

- you can pass many option to the `mount -o` switch

Sure, here's an explanation of each of these Bash commands along with common examples:

### `stat`:

- `stat` is a command-line utility used to display detailed information about file systems, files, and directories.
- It provides metadata about files such as size, permissions, timestamps, and more.
- `stat example.txt` to view information about a file named `example.txt`

### `vmstat`:

- `vmstat` stands for virtual memory statistics. It reports information about processes, memory, paging, block IO, traps, and CPU activity.
- It's particularly useful for monitoring system performance and identifying potential bottlenecks.
- `vmstat 1` to display system-wide information with a delay of 1 second, you can use:

### `strace`:

- `strace` is a debugging utility in Unix-like operating systems that intercepts and records the system calls and signals made by a process.
- It's commonly used to diagnose and troubleshoot problems related to system calls and to understand the behavior of programs.
- `strace ls` to trace the system calls made by the `ls` command

### `ulimit`:

- `ulimit` is a built-in command in Unix-like operating systems used to control the resources available to processes started by the shell.
- It can limit various system resources such as CPU time, memory, file size, and more.
- `ulimit -a` To display current resource limits, you can use:

These commands are essential for system administrators, developers, and power users to monitor system performance, diagnose issues, and manage resource usage efficiently in Unix-like environments.

## Extracting and manipulating data

- a very common pattern in unix is to take some data (text file, directory listing, output from a command) and either extract specific data from it, change some of the dat, or both. these tools help you do that:

### `cat`

- `cat /tmp/foo.txt` print the contents of a file to the shell
- `cat /tmp/foo.txt /tmp/bar.txt` print out the contents of each file in the same order as the files
- `cat /tmp/foo.txt /tmp/bar.txt > /tmp/foobar.txt` combine the combine the contents into a new file (and create that file)
- WARNING: `cat` overwrites the contents of a file

### `cut`

- `cut` cuts out selected portions of each line and writes them to standard output

```sh
cat students.txt
John Doe 25 john@example.com
Jack Smith 26 jack@example.com
Jane Doe 24 jane@example.com
```

- `-f` choose the fields we want to output

```sh
cut -f1,4 students.txt
John john@example.com
Jack jack@example.com
Jane jane@example.com
```

- `cut -fl d| students.txt` define the delimiter with `-d` in this case `|`

```sh
cut -f1 -d'|' students.txt
John Doe
Jack Smith
Jane Doe
```

```sh
cut -f1 -d' ' students.txt
John
Jack
Jane
```

- if you have input with fixed width columns you can use `-c` to break them apart

```sh
who | cut -c 1-9,33-38
baz       18:21
baz       18:21
```

### `grep`

- `grep` matches patterns in files.
- it's name comes from the `ed` command g/re/p globally search a regular expression and print the results
- `grep` looks for given pattern in the specified file/files and prints all lines which match
