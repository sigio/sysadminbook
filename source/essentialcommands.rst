Essential Shell Commands
========================

In the following chapters of this book we will run into very many different commands, and it's not possible to explain all commands before then appear in some examples or are needed for some tasks we will run into. This chapter will describe some of the most-often used commands and occasionally give example output.

We will start with some commands that are used to navigate the filesystem and inspect files:

======= =========================== ======================
Command Function                    Safe Example
======= =========================== ======================
cd      Change directory            cd /usr/local/bin
pwd     Show current directory      pwd
cat     Show contents of a file     cat /etc/passwd
less    Page though a file          less /etc/services
ls      Show directory listing      ls -alF /
======= =========================== ======================

cd and pwd
----------

.. code-block:: bash
  :emphasize-lines: 1,3,4,6,20,21

  $ pwd
  /home/yourname
  $ cd /usr/local
  $ pwd
  /usr/local
  $ ls -al
  total 0
  drwxr-xr-x. 12 root root 131 Nov 23 18:31 .
  drwxr-xr-x. 13 root root 155 Nov 23 18:31 ..
  drwxr-xr-x.  2 root root   6 Apr 11  2018 bin
  drwxr-xr-x.  2 root root   6 Apr 11  2018 etc
  drwxr-xr-x.  2 root root   6 Apr 11  2018 games
  drwxr-xr-x.  2 root root   6 Apr 11  2018 include
  drwxr-xr-x.  2 root root   6 Apr 11  2018 lib
  drwxr-xr-x.  2 root root   6 Apr 11  2018 lib64
  drwxr-xr-x.  2 root root   6 Apr 11  2018 libexec
  drwxr-xr-x.  2 root root   6 Apr 11  2018 sbin
  drwxr-xr-x.  5 root root  49 Nov 23 18:31 share
  drwxr-xr-x.  2 root root   6 Apr 11  2018 src
  $ cd
  $ pwd
  /home/yourname

As can be seen in the example above, the **pwd** command will print the directory we are currently in back to the shell, and the **cd** command can be used to change to a different directory. If you run the **cd** command with an argument, you change to the specified directory, when running **cd** without any arguments, it will bring you to your *HOME* directory. The *HOME* directory is the directory you enter when you login to a system and where you can write your own files.

To navigate to various directories you can either specify a complete and absolute path, or use relative paths. An absulute path is a path that starts with a **/** character, denoting the *root* of the filesystem. In the example above, **/usr/local** is an absolute path. In the example below we will use relative paths:

.. code-block:: bash
  :emphasize-lines: 1,3,4,6,7,8,10,11

  $ pwd
  /home/yourname
  $ cd ..
  $ pwd
  /home
  $ cd /usr/local/lib
  $ cd ../bin
  $ pwd
  /usr/local/bin
  $ cd ~
  $ pwd
  /home/yourname

By using *..*, you indicate that you want to go up one level in the directory-tree, combining this with other directory names you can navigate back down other branches of the tree. If you are not to familiar with this method of navigating the filesystem you can always use absolute paths as an alternative method of navigating, but this will usually require some more typing.

cat and less
------------

The **cat** command (which is short for concatinate), can be used to concatinate files, but it's mostly used to get the contents of a file printed back to your terminal. If you specify multiple files as arguments to the **cat** command, all files will be printed to the terminal one after the other, as if they were 1 continuous file (as such, they have been concatinated before being sent to your terminal).

.. code-block:: bash
  :emphasize-lines: 1,3,12

  $ cat /etc/redhat-release
  CentOS Linux release 7.5.1804 (Core) 
  $ cat /etc/shells 
  /bin/sh
  /bin/bash
  /sbin/nologin
  /usr/bin/sh
  /usr/bin/bash
  /usr/sbin/nologin
  /bin/tcsh
  /bin/csh
  $ cat /etc/redhat-release /etc/shells 
  CentOS Linux release 7.5.1804 (Core) 
  /bin/sh
  /bin/bash
  /sbin/nologin
  /usr/bin/sh
  /usr/bin/bash
  /usr/sbin/nologin
  /bin/tcsh
  /bin/csh

If you **cat** a larger file, or multiple files at the same time, the contents of these files will fly over your screen quickly. If you want to actually have the possibility of reading the contents, it's better to use what's called a *pager*. Linux comes with multiple alternative pagers, classical UNIX systems had the *more* pager, which you can also find on most Linux systems. However there is usually also a pager called **less**. The *less* pager will print the contents of the
file one screen-full at a time and will either scroll a single line, or a full screenlength up or down through the file when you press one of the arrow-keys, page-up or page-down or the space key. If you want to exit less, you can press the **q** to exit back to your shell. Another handy feature of *less* is that it can be used to search for text though a file, when entering **/sometext** it will scroll to the next occurance of *sometext* and highlight the text in the file.

ls
--

The **ls** command is used to get information about files and directories, compare it with the **DIR** command in for example DOS and Windows systems. If you just run **ls** it will show a short listing of all files and directories in the current working directory. Specifying various arguments to the *ls* command will alter the way this information is presented. The most used options to *ls* are:

-a          Show hidden files and directories (names starting with a dot)
-l          Show a long listing, one name per line, with permission, size and date information
-F          Classify names as either directory, regular file or executable


File manipulation commands
--------------------------

Next up are some file-manipulation commands:

======= =========================== ===============================
Command Function                    Safe Example
======= =========================== ===============================
cp      Copy a file                 cp /etc/passwd ~
mv      Move or rename files        mv ~/passwd ~/renamed
rm      Remove a file               rm ~/renamed
mkdir   Create a directory          mkdir /tmp/myfiles
rmdir   Remove (empty) directories  rmdir /tmp/myfiles
======= =========================== ===============================

These basic file manipulation commands will let you copy, erase and move-around files or create and remove directories. The **cp** and **mv** commands need at least two arguments, a source and a target to work on. If you specify more then 2 arguments, and the last argument is a directory, all other arguments are considered as source, and multiple files will be copied or moved to the specified target directory.

.. sidebar:: ⚠️ rmdir vs rm -r

  As stated, the **rmdir** command will only remove empty directories. If you want to remove a directory, and all files and other directories within this directory, you can use the **rm -r** command. But be careful, if used carelessly, it can delete large amounts of files before you notice any possible mistake. If using **rm -r** and not feeling comfortable, add the **-i** option, which asks for confirmation before deleting files and directories.

  Example: **rm -i -r /tmp/mydirectory**

.. code-block:: bash
  :emphasize-lines: 1,3,4,6,20,21

  $ mkdir /tmp/mydirectory
  $ ls -la /tmp/mydirectory/
  total 0
  drwxrwxr-x.  2 yourname yourname   6 Nov 24 03:21 .
  drwxrwxrwt. 10 root     root     253 Nov 24 03:21 ..
  $ cp /etc/issue /etc/issue.net /etc/passwd /tmp/mydirectory
  $ ls -la /tmp/mydirectory/
  total 12
  drwxrwxr-x.  2 yourname yourname   50 Nov 24 03:21 .
  drwxrwxrwt. 10 root     root      253 Nov 24 03:21 ..
  -rw-r--r--.  1 yourname yourname   23 Nov 24 03:21 issue
  -rw-r--r--.  1 yourname yourname   22 Nov 24 03:21 issue.net
  -rw-r--r--.  1 yourname yourname 2205 Nov 24 03:21 passwd
  $ cd /tmp/mydirectory
  $ rm issue issue.net
  $ ls -la
  total 4
  drwxrwxr-x.  2 yourname yourname   20 Nov 24 03:22 .
  drwxrwxrwt. 10 root     root      253 Nov 24 03:21 ..
  -rw-r--r--.  1 yourname yourname 2205 Nov 24 03:21 passwd
  $ rm /tmp/mydirectory/passwd
  $ rmdir /tmp/mydirectory

