# Package management

## Workflow

## What is a package manager?

Modern operating systems use package managers to take care of the
installation, maintenance and removal of software. On Windows this is
[Windows Installer](http://en.wikipedia.org/wiki/Windows_Installer)
(formerly Microsoft Installer). On Linux there are two popular package
managers:

- APT (used by Debian, Ubuntu)
- RPM (RedHat, CentOS, Fedora, SuSe)

Specific commands for each system vary, but at their core they all
provide the same functionality:

- Install and uninstall packages
- Upgrade packages
- Install packages from a central repository
- Search for information on installed packages and files

## RPM and yum (RedHat, CentOS, Fedora, Scientific Linux)

In the following examples, we will be using `dstat` as the package we
will be manipulating. The process however applies to any software you
may want to install.

Yum provides a wrapper around RPM, which can be used to search for, and
install packages from multiple package repositories. It also resolves
dependencies, so that if a package has prerequisites, they will be
installed at the same time.

If your Linux distribution uses RPM and yum, you can search for packages
by running:

```sh
user@opsschool $ yum search dstat
======================== N/S Matched: dstat =========================
dstat.noarch : Versatile resource statistics tool
```

### Installing packages

You can install a package using yum, by running:

```sh
root@opsschool $ yum install dstat

=============================================================================
 Package        Arch            Version              Repository         Size
=============================================================================
Installing:
 dstat          noarch          0.7.0-1.el6          CentOS-6          144 k

Transaction Summary
=============================================================================
Install       1 Package(s)

Total download size: 144 k
Installed size: 660 k
Is this ok [y/N]:
```

If you have a downloaded RPM file, you can also install the package
directly with the `rpm` command:

```sh
root@opsschool $ rpm -i dstat-0.7.0-1.el6.noarch.rpm
```

### Upgrading packages

RPM and yum both make it easy to upgrade existing packages, too. Over
time, new packages may be added to the yum repositories that are
configured on your system, or you may have a newer RPM for an already
installed package.

To upgrade a package using yum, when a newer package is available,
simply ask yum to install it again:

```sh
root@opsschool $ yum install dstat
```

To upgrade all packages that have newer versions available, run:

```sh
root@opsschool $ yum upgrade
```

To upgrade a package with an RPM file, run:

```sh
root@opsschool $ rpm -Uvh dstat-0.7.1-1.el6.noarch.rpm
```

### Uninstalling packages

To uninstall a package using yum, run:

```sh
root@opsschool $ yum remove dstat
```

Similarly, you can uninstall a package with rpm:

```sh
root@opsschool $ rpm -e dstat
```

### Cleaning the RPM database

You can clean the RPM database, forcing it to refresh package metadata
from its sources on next install or upgrade operation.

```sh
root@opsschool $ yum clean all
```

### Querying the RPM database

Occasionally you will want to find out specific information regarding
installed packages. The `-q` option to the `rpm` command comes in handy
here. Let's take a look at a few examples:

One common task is to see if you have a package installed. The `-qa`
option by itself will list ALL installed packages. You can also ask it
to list specific packages if they are installed:

```sh
user@opsschool $ rpm -qa dstat
dstat-0.7.0-1.el6.noarch
```

Now let's say we want to list all of the files installed by a package.
The `-ql` option is the one to use:

```sh
user@opsschool $ rpm -ql dstat
/usr/bin/dstat
/usr/share/doc/dstat-0.7.0
/usr/share/doc/dstat-0.7.0/AUTHORS
/usr/share/doc/dstat-0.7.0/COPYING
/usr/share/doc/dstat-0.7.0/ChangeLog
...
```

We can also do the reverse of the previous operation. If we have a file,
and want to known which package it belongs to:

```sh
user@opsschool $ rpm -qf /usr/bin/dstat
dstat-0.7.0-1.el6.noarch
```

### Creating packages

## dpkg and APT (Debian, Ubuntu)

In the following examples, we will be using `dstat` as the package we
will be manipulating. The process however applies to any software you
may want to install.

APT provides a wrapper around `dpkg`, which can be used to search for,
and install packages from multiple package repositories.

If your Linux distribution uses `dpkg` and APT, you can search for
packages by running:

```sh
user@opsschool $ apt-cache search dstat
dstat - versatile resource statistics tool
```

```sh
baz@baz-pc:~$ apt-cache search dstat
sysstat - system performance tools for Linux
dstat - versatile resource statistics tool
```

In the following sections, we will be describing use of APT through the
traditional commands of `apt-get` and `apt-cache`. However, modern
systems may ship with an additional command `apt` which (generally)
combines the functionality of both of the above commands. If your system
supports `apt` by itself, it is recommended to use that, as it is a bit
more user friendly.

### Installing packages

You can install a package through apt, by running:

```sh
root@opsschool $ apt-get install dstat

The following NEW packages will be installed:
  dstat
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 0 B/79.3 kB of archives.
After this operation, 351 kB of additional disk space will be used.
Selecting previously unselected package dstat.
(Reading database ... 124189 files and directories currently installed.)
Unpacking dstat (from .../archives/dstat_0.7.2-3_all.deb) ...
Processing triggers for man-db ...
Setting up dstat (0.7.2-3) ...
```

```sh
baz@baz-pc:~$ sudo apt install dstat
[sudo] password for baz:
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following NEW packages will be installed:
  dstat
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 55.6 kB of archives.
After this operation, 466 kB of additional disk space will be used.
Get:1 http://gb.archive.ubuntu.com/ubuntu mantic/universe amd64 dstat all 0.7.4-6.1 [55.6 kB]
Fetched 55.6 kB in 0s (970 kB/s)
Selecting previously unselected package dstat.
(Reading database ... 238298 files and directories currently installed.)
Preparing to unpack .../dstat_0.7.4-6.1_all.deb ...
Unpacking dstat (0.7.4-6.1) ...
Setting up dstat (0.7.4-6.1) ...
Processing triggers for man-db (2.11.2-3) ...
baz@baz-pc:~$
```

If you have a downloaded DEB file, you can also install the package
directly with the `dpkg` command:

```sh
root@opsschool $ dpkg -i dstat_0.7.2-3_all.deb
```

### Upgrading packages

dpkg and APT both make it easy to upgrade existing packages, too. Over
time, new packages may be added to the apt repositories that are
configured on your system, or you may have a newer deb for an already
installed package.

In order to retrieve the updated package lists, first run:

```sh
root@opsschool $ apt-get update
```

To upgrade a single package using apt, when a newer package is
available, simply ask apt to install it again:

```sh
root@opsschool $ apt-get install dstat
```

To upgrade all packages at once, run:

```sh
root@opsschool $ apt-get upgrade
```

To upgrade a package with an deb file, run:

```sh
root@opsschool $ dpkg -i dstat_0.7.2-3_all.deb
```

### Uninstalling packages

To uninstall a package using apt, run:

```sh
root@opsschool $ apt-get remove dstat
```

Similarly, you can uninstall a package with dpkg:

```sh
root@opsschool $ dpkg -r dstat
```

With APT and dpkg, removing a package still leaves behind any
configuration files, in case you wish to reinstall the package again
later. To fully delete packages and their configuration files, you need
to `purge`:

```sh
root@opsschool $ apt-get purge dstat
```

or:

```sh
root@opsschool $ apt-get --purge remove dstat
```

or:

```sh
root@opsschool $ dpkg -P dstat
```

### Querying the dpkg database

Occasionally you will want to find out specific information regarding
installed packages. The `dpkg-query` command has many options to help.
Let's take a look at a few examples:

One common task is to see if you have a package installed. The `-l`
option by itself will list ALL installed packages. You can also ask it
to list specific packages if they are installed:

```sh
user@opsschool $ dpkg-query -l dstat
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name           Version      Architecture Description
+++-==============-============-============-==================================
ii  dstat          0.7.2-3      all          versatile resource statistics tool
```

Now let's say we want to list all of the files installed by a package.
The `-L` option is the one to use:

```sh
user@opsschool $ dpkg-query -L dstat
/.
/usr
/usr/bin
/usr/bin/dstat
/usr/share
...
```

We can also do the reverse of the previous operation. If we have a file,
and want to know to which package it belongs:

```sh
user@opsschool $ dpkg-query -S /usr/bin/dstat
dstat: /usr/bin/dstat
```
