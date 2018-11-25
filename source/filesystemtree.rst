.. MIT License
   Copyright © 2018 Sig-I/O Automatisering / Mark Janssen, Licensed under the MIT license

File System Hierarchy
=====================

UNIX and Linux systems present all the files available to the system in a single file
hierarchy, which can be viewed as a tree, branching out into directories and multiple
levels of subdirectories.

The various directories under the root of the tree all have their own function and
purpose, though there are still some remnants from the beginnings of UNIX which are slowly
being replaced or migrated to a cleaner and more modern setup.

You can inspect the various directories and files with the **ls** and **cd** commands, as
we have discussed, but if's also possible to get a quick overview by using commands like
**tree** or **find / -type d -maxdepth 4**, or by browsing the filesystems using a
file-manager such as **nc** or the **nautilus** filemanager which is used by the GNOME
desktop environment.

Looking at the directories directly under the root of the filesystem (*/*), we will see
the following (on a CentOS 7 system).

.. code-block:: bash
  :emphasize-lines: 1

  $ ls -F /
  bin@   dev/  home/  lib64@  mnt/  proc/  run/   srv/  tmp/  var/
  boot/  etc/  lib@   media/  opt/  root/  sbin@  sys/  usr/

While on a Debian 9.x system we will see:

.. code-block:: bash
  :emphasize-lines: 1

  $ ls -F /
  bin/    etc/            lib/    mnt/    root/   srv/    var/
  boot/   home/           lib64/  opt/    run/    tmp/    vmlinuz@
  dev/    initrd.img@     media/  proc/   sbin/   usr/

As can be seen, there are some slight differences, which we weill get to in a moment, but
mostly both systems have the same directory-structure.

The **/boot** directory holds the files needed to start the Linux kernel. Here you will
find files related to the GRUB bootloader, and one or more sets of a kernel file (vmlinuz)
and initramfs (initrd on Debian) files with matching versions. On most systems /boot will
actually be on a different filesystem from the root, and you will usually find this to be
/dev/sda1 or /dev/vda1 or something equivalent.

.. sidebar:: A History Lesson

  Historically, the source of many of the directories present in the root of a UNIX or
  Linux system can be traced back to the early beginnings of UNIX. When UNIX was
  developped, the disks used in the available systems were very small (1.5MB). The trick
  was to keep UNIX small enough that it would fit on a single disk.

  As UNIX got bigger and bigger more storage was needed, and a second disk was added,
  and mounted under /usr. This used to be the directory where the home-directories for the
  users were kept (hence the name). By moving the users' files to the second disk, more
  space became available on the root disk to store programs.

  Eventually though, this was no longer enough, and the root-disk outgrew it's available
  capacity again. The early UNIX developers decided to move some larger programs from
  /bin, to a newly created /usr/bin directory. The trick however was to only move binaries
  to this new directory that were not needed to get the system booted, as during bootup,
  the second (/usr) disk was not yet available.

  Even later, when the /usr disk was also running out of space, a third disk was added,
  and the user-files were moved to /home, their current location, leaving /usr and the
  root disk for the OS itself.

  Now, almost 50 years later, much of this historic data-moving is still being preserved,
  even though disks have been more then big enough to hold the entire operaring system on
  a single drive for ages. Red Hat (and other distributions) are slowly working on getting
  rid of some of this historic cruft, by replacing /bin and /sbin with symlinks to
  /usr/bin and /usr/sbin, Eventually we should be able to finally say goodbye to some
  duplicate directories.

The **/bin** directory holds binaries that are used early in the boot process. On
CentOS/RHEL 7 systems and later, this will be a symlink to /usr/bin, On Debian 9 and
CentOS 6.x this will still be it's own directory. This directory is included in the PATH
on systems where it is still used. (RHEL 6.x, Debian, Ubuntu)

Next up, the **/dev** directory holds device-files. Here you will find files used to
interact with the hardware in the system, there are files for all detected disks,
storage-devices, soundcards, webcams, serial ports, etc.

The **/etc** directory is used to store various configuration files used by the system,
for example /etc/passwd, /etc/group, /etc/services and many others

**/home** holds the home-directories for the users on the system. Likewise **/root** can
be seen as the home-directory for the sysadmin account *root*.

**/lib** and **/lib64** contain library-files used by the system, historically there was
only /lib, but with the migration to 64-bit systems, /lib64 was added, as 64-bit systems
had the possibility to run 32-bit applications besides 64-bit applications, but these
would then need 2 sets of library-files installed. On CentOS 7 and beyond, both these
directories are symlinks pointing to their replacements in /usr/lib and /usr/lib64.

**/media** is an empty mointpoint, used by the Desktop Environment to mount hot-pluggable
storage devices such as CD-ROM's, USB-Sticks etc.

**/mnt** is also an empty mountpoint, which can be used by the sysadmin to temporarily
mount external storage devices or network storage devices.

**/opt**, another empty by-default directory can be used as a location to install
third-party software. LInux distributions will not touch this directory with their
package-management software.

**/proc**, is a special filesystem that only exists in memory and is populated on-the-fly
when accessed by a user or process. This filesystem contains information-files about
the system, hardware and running processes. Some system-settings can be tweaked by writing
certain values to specific files under /proc.

**/run** is a tmpfs type filesystem. This means that it also only exists in the memory of
a running system, but files can be stored here while the system is running. If the system
crashes or reboots, the files stored are lost. This filesystem is used to store small
files used in some housekeeping functions of running processes. Most files here will be
used as *PID* files or as *lock-files*.

**/sbin**, like the /bin directory, holds executables, or binaries, but the /sbin
directory isn't present in the PATH of a normal user by default. The programs in this
directory are mostly meant for use by the *root* user or the system itself.

**/srv** will usually be empty as well, but can be used by some software to hold files
related to services provided by the system, for example, the files for the tftp and ftp
services, or web-content from some webservers. On RHEL and CentOS this directory isn't
used often.

A much used directory, **/tmp** will hold temporary files. This directory is writable by
all users on the system, and will be used by various processes to temporarily store files.
Some systems will actually store /tmp in memory, which guarantees that it will be empty
after a restart, but both Debian and CentOS currently store data saved here on disk at
this time if using the default settings. Usually there are however scripts and systems to
cleanup older files stored in /tmp to prevent it running out of hand.

One of the bigger directories, **/usr**, holds many directories with the same name we have
already seen in the root, you will find for example /usr/lib, /usr/bin and /usr/sbin here.
Most files under /usr are part of programs installed on the system, either as executable
program, library or part of the data-files that belong to these programs.

The last directory **/var** also has many subdirectories. /var is mostly used for
data-files, files that will change when programs are used, such as databases or
cache-files for the package-management system. Historically, the mail-files for users are
also kept in /var/mail.

.. index:: cd -

Navigating the directory tree efficiently
-----------------------------------------

There are several commands that make your life easier when moving around a lot between
different locations in the filesystem tree.

As we have seen, the **cd** command is used to change directories. Using the **cd -**
argument you can quickly go to the previous directory you were in. So if you need to go
back and forth between 2 paths, **cd -** will get the job done quickly.

.. code-block:: bash
  :emphasize-lines: 1,2,4,5,7,8,10,11

  $ cd /usr/local/bin
  $ pwd
  /usr/local/bin
  $ cd /var/spool/mail
  $ pwd
  /var/spool/mail
  $ cd -
  $ pwd
  /usr/local/bin
  $ cd -
  $ pwd
  /var/spool/mail

.. index:: pushd, popd, dirs

Another usefull set of commands to know are **pushd**, **popd** and **dirs**. Using the
pushd command, you can change to a directory and also push this directory to a stack, you
can then later go back to this pushed directory by using **popd**, which pops (removes) a
directory from the stack and goes there. You can add multiple directories on the stack, and
on every invocation of popd, the next directory will be removed again. The **dirs** command
will show the current directory stack, with the first entry always being the current directory.

.. code-block:: bash
  :emphasize-lines: 1,2,4,6,8,10,12,14,16,18,20

  $ cd ~
  $ pushd /usr/local/bin
  /usr/local/bin ~
  $ pwd
  /usr/local/bin
  $ pushd /var/spool/mail
  /var/spool/mail /usr/local/bin ~
  $ pwd
  /var/spool/mail
  $ dirs
  /var/spool/mail /var/spool/mail /usr/local/bin ~
  $ pushd ~
  ~ /var/spool/mail /usr/local/bin ~
  $ popd
  /var/spool/mail /usr/local/bin ~
  $ popd
  /usr/local/bin ~
  $ pwd
  /usr/local/bin
  $ popd
  ~

Another trick you can use, if there are directories where you often end up going, is to
create an alias or variable for this directory.

.. code-block:: bash
  :emphasize-lines: 1,2,3,5,6,7

  $ alias cdproj='cd /usr/local/share/applications'
  $ cdproj
  $ pwd
  /usr/local/share/applications
  $ export ULB=/usr/local/bin
  $ cd $ULB
  $ pwd
  /usr/local/bin

.. index:: find, locate, updatedb

Finding files efficiently
-------------------------

You can use the **find** command to look for files on the system that match some
requirements you tell the find command. You can for example find all files with a matching
name, or files over a specific filesize, files from a specific user and many more things.

The **find** command has a quite complex syntax, so be sure to check out the manpage if
you are looking for the best way to search for your file. The find command will look for
the file by reading all directories on the system and matching all files against your
requirements. This can be quite slow, especially on systems with large amounts of files.

If you are only looking for files based on their name, and you want results quickly, you
can try the **locate** command first. Locate works by building a database of all the files
once a day or once a week, and then looking for the files in this pre-built database. This
will usually give you your results in less then a second, compared to sometimes minutes
with the find command. The disadvantage however, is that locate can only find files based
on their name, and only files that were actually present on the system at the time the
database was last updated, so you won't find any files created just recently.

As root, you can force a re-index of all the files using the **updatedb** command. This
will then rebuild the database, and locate will have access to all newly scanned files.

.. code-block:: bash
  :emphasize-lines: 1

  $ touch ~/some-new-file       # create a new file
  $ locate some-new-file        # no results returned
  $ find / -name '*some-new-file*' 2> /dev/null
  /home/yourname/some-new-file

  # updatedb                    # re-index the filesystem

  $ locate some-new-file        # we get results now
  /home/yourname/some-new-file


