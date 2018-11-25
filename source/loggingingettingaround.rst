.. MIT License
   Copyright © 2018 Sig-I/O Automatisering / Mark Janssen, Licensed under the MIT license

Logging in and getting around with Linux
========================================

Now that we have a Linux system installed, we can login and start using it. If you have
installed a Linux system without a GUI, you will be greeted by the 'login' program,
running on a text-console. This will look somewhat like the following:

.. code-block:: none

  CentOS Linux 7 (Core)
  Kernel 3.10.0-862.el7.x86_64 on an x86_64

  centos7 login:

You can enter the username to your account, or use the *root* account. On the following
password prompt, enter the password for the account, and you will be presented with a
shell. Note that the text entered after the password prompt is not printed to the screen
to prevent leaking your password to someone looking over your shoulder.

.. code-block:: none

  centos7 login: yourname
  Password:
  Last login: Sat Nov 24 12:34:56 on tty1
  [yourname@centos7 ~]$

If you specify an incorrect password, or if the useraccount you are trying to use doesn't
actually exist, the system will only tell you *Login incorrect*, and present you with a
new *login:* prompt. This is done so the system doesn't leak information about available
accounts.

The Virtual Teletype
--------------------

.. sidebar:: The Teletype

  .. image:: _static/images/teletype.jpg
     :alt: The 'original' teletype hardware
     :target: https://commons.wikimedia.org/wiki/File:Teletype.jpg

  The virtual teletype is basically the software equivalent of the original hardware-based
  teletypes, consisting of a keyboard and a printer. Typed text would be sent to the
  computer, output from the computer would be printed out on either a paper-tape or on
  fan-folded paper. These devices were in use before monitors were commonly available, and
  were usually connected via a Serial interface. The linux consoles, virtual teletypes and
  serial-connections still emulate and work as if they were connected to a teletype, but
  now with input coming from your keyboard, and output going to a monitor or screen
  instead of paper.

If you see the login prompt (or a shell session) on a linux console session, you are in
fact looking at the console or (virtual) teletype (abbreviated as *tty*). Most linux
systems will expose multiple virtual teletypes for the user to work with. If you run the
**tty** command, it will tell you on which virtual teletype you are currently connected.
You can switch to a different tty by pressing *Alt-Left* or *Alt-Right*, or *Alt-F1*
through *Alt-F7*. Alternatively, the **chvt** command can be used to switch to a the named
tty:

.. code-block:: bash
  :emphasize-lines: 1,3

  $ tty
  /dev/tty2
  $ chvt 4
  (view will switch to a new login-session on /dev/tty4)

Centos and RHEL systems will usually have the Graphical Environment running on the first
tty, /dev/tty1, which is reached with Alt-F1, Debian and Ubuntu systems usually have their
Graphical Environment running on /dev/tty7, which can then be viewed by pressing Alt-F7.
When already in a Graphical Environment, you need to add the Ctrl-button to the
key-combination to go back to a text-only environment.

Possible uses of multiple tty's include for example viewing a logfile on a tty, while
editing a configuration file for a service on a second tty, and issueing restart commands
on a third tty. Using this method it's easy to quickly switch between different tasks,
even if these are running under different useraccounts. If running with a graphical
interface, this is usually not needed, as multiple *terminal emulator windows* can be
opened and viewed next to eachother.

Becoming Root
-------------

For most system-administration tasks you will need to have root privileges. It's
considered best-practice however to limit usage of the root account as much as possible.
To reconsile these two statements, it's best to always login as yourself, and gain
administrative privileges only when needed. You can for example use the **sudo -s**
command to start a new shell as *root*, if you have sudo-permissions. Alternatively you
can run single commands in the context of the root user by pre-pending the command with
sudo, for example:

.. code-block:: bash

  $ sudo id

.. image:: _static/images/centos-7-first-use-sudo.png
   :alt: Logging in and using Sudo to become root

.. sidebar:: First time using sudo

  The first time that you run sudo, you will be greeted with the text: *We trust you have
  received....* The next times you run sudo, it will only ask for your password. If you
  have run sudo successfully in the last 5 minutes (by default) you will not have to
  re-enter your password.

As you can see, after running the **sudo -s** command, the prompt has changed from
*[yourname@centos7 ~]$* to *[root@centos7 yourname]#*. The prompt will always show you
some critical information about who you are, where you are in the filesystem and if you
are *root* or not.

.. index:: prompt, $PS1

The Prompt
----------

The first part of the prompt is your current username, in the example here *yourname*.
Then, after the @ sign, is the hostname of the system you are currently logged in on.
Since Linux systems are usually connected to over a network, and it's easy to hop from one
system to another, it's really important to always have a visual indication on what system
you currently are, to prevent you from doing something on the wrong system. The third bit
of the prompt, here *~* initially and *yourname* after the sudo command, tells you where
you are in the filesystem. By default CentOS and RHEL put the name of the current
directory in the prompt. Some other systems put the entire path here, which will result in
a longer prompt if you are deep in a directory structure.

The last part of the prompt will usually be a '$' or '#' sign. Normal users will have a
'$' sign, and the root-user will always have the '#' sign. In future examples, prompts
will usually start with either an '$' or an '#' sign to indicate that the command should
be run as regular user or as root.

Using the shell
---------------

When using Linux as a systems administrator you will be mostly working in the shell. The
default shell used by most Linux distributions is *bash*, though other shells are
available, other popular choices are for example *zsh* and *fish*. These shells are all
focussed on interactive use, but there are also shells more focussed on usage in scripts,
such ash dash or ash, which you can find in use on various Debian based scripts.

The shell is the main command interpreter that is started after logging in to a system.
It's job is to read commands from the user, execute the commands either in the foreground
or the background and then wait for a new command to be executed. The shell uses various
environment variables which can influence what the shell does, or how it interprets the
commands it's given.

The commands a shell interprets can be deviced in the following categories:

* Aliases
* Internal Commands
* External Commands

.. index:: aliases, alias

Aliases
^^^^^^^

An alias is basically a shortcut command, where the typed command is replaced by some
other command which will be executed. This can for example be used to add specific options
to existing commands, or to define a short command which executes something which would
require typing a long and complex command.

.. code-block:: none
  :emphasize-lines: 1

  $ alias
  alias egrep='egrep --color=auto'
  alias fgrep='fgrep --color=auto'
  alias grep='grep --color=auto'
  alias l.='ls -d .* --color=auto'
  alias ll='ls -l --color=auto'
  alias ls='ls --color=auto'
  alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'

.. sidebar:: Aliases for the root user on CentOS and RHEL

  On these systems, the root user has a few default aliases, which replace the *rm*, *mv*
  and *cp* commands with aliases that include the *-i* option. This causes these commands
  to ask for confirmation before executing their respective tasks.

  This is done to help protect beginners from accidentally changing files on their system.
  When you are more comfortable in working with the shell it's suggested to remove these
  aliases.

On CentOS and RHEL systems various aliases are present in the default configuration, these
can be inspected by typing the command *alias*. As you can see in the image above, there
is an alias configured which says: **alias ll='ls -l --color=auto'**. 

This alias means that if you would enter the **ll** command, the shell would instead
execute **ls -l --color=auto**. This also works if argument are given to the command, so
running:

.. code-block:: bash

  $ ll /boot

would result in the shell actually running:

.. code-block:: bash

  $ ls -l --color=auto /boot

And in the shell you would then see something like the following (but with some file or
directory names in various colors)

.. code-block:: none
  :emphasize-lines: 1

  [yourname@centos7 ~]$ ll /boot
  total 91224
  -rw-r--r--. 1 root root   147819 Apr 20  2018 config-3.10.0-862.el7.x86_64
  drwxr-xr-x. 3 root root       17 Nov 23 18:31 efi
  drwxr-xr-x. 2 root root       27 Nov 23 18:32 grub
  drwx------. 5 root root       97 Nov 23 18:44 grub2
  -rw-------. 1 root root 55392447 Nov 23 18:33 initramfs-0-rescue-677554f79ad949f09199a21f019d4613.img
  -rw-------. 1 root root 21692607 Nov 23 18:44 initramfs-3.10.0-862.el7.x86_64.img
  -rw-r--r--. 1 root root   304926 Apr 20  2018 symvers-3.10.0-862.el7.x86_64.gz
  -rw-------. 1 root root  3409143 Apr 20  2018 System.map-3.10.0-862.el7.x86_64
  -rwxr-xr-x. 1 root root  6224704 Nov 23 18:33 vmlinuz-0-rescue-677554f79ad949f09199a21f019d4613
  -rwxr-xr-x. 1 root root  6224704 Apr 20  2018 vmlinuz-3.10.0-862.el7.x86_64

You can define your own aliases with the **alias** command. These aliases will only work
in the currently running shell session. To keep aliases for new shell invocations they
need to be configured in your *profile* or *.bash.rc* file.


.. index:: builtins, alias, bg, cd, echo, exec, exit, export, false, fg, history, jobs, kill, pwd, read, set, source, test, true, ulimit, umask, unalias, unset, wait

Internal Commands
^^^^^^^^^^^^^^^^^

Internal commands are commands that are implemented as functions in the shell itself, and
therefor do not have to be present as executables on the disk. Various shells have their
own sets of internal commands, though some commands will always be implemented as internal
commands. For the bash shell, you can find the list of built-in commands by looking at the
*builtins* manual-page. The following list of built-in commands contains some often used
internal commands:

=============   ===================================================
Builtin         Function
=============   ===================================================
alias           show or define aliases
bg              background a job
cd              Change directory
echo            Display contents of a variable
exec            Execute a command in place of the current shell
exit            Exit the current shell
export          Export a variable definition
false           Return a false value
fg              Take a background job back to the foreground
history         Look at commands entered before
jobs            Show jobs started by this shell
kill            Send signals to processes
pwd             Print current/working directory
read            Read input from the user
set             Change a setting
source          Read commands from a file
test            Execute a test
true            Return a true value
ulimit          Show or change resource limits
umask           Show or change filemasks
unalias         Unset aliases
unset           Unset settings
wait            Wait for a process to exit
=============   ===================================================

External Commands
^^^^^^^^^^^^^^^^^

The last group of commands are the *external commands*. These are basically all the other
commands interpreted by the shell. External commands always refer to executable files or
scripts present on the storage of the system. If the shell reads a command, it will first
determine if the given command is an alias or internal-command and when this is not the
case, it will search for the command on the filesystem.

The shell will not just scan the entire disk for possible commands, but will only look for
locations specified in it's *PATH*. The *PATH* variable lists the various directories
which are scanned in order for matching commands. If a command is present in multiple
locations in the path, the first matched version will be executed.

You can inspect your current **PATH** variable using the built-in **echo** command. Shell
variables are referenced using the variable-name prefixed with the **$** sign:

.. code-block:: bash
  :emphasize-lines: 1

  $ echo $PATH
  /usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/yourname/ ...

Alternatively, you can inspect all shell variables with either the **set** builtin, or the
**env** external command.

Note that the root user has different directories in their default PATH then non-root
users, and every user is free to change their path temporarily of permanently to suit
their needs. If you want to run a command that can be found on more then one location on
the system, or that also exists as a builtin, you can run it by explicitly specifying it's
full (or relative) path as part of the command. For example, here we first use the
*builtin* **echo** command, and then the *external* **echo** command.

.. index:: echo

.. code-block:: bash
  :emphasize-lines: 1,3

  $ echo "Hello World"
  Hello World
  $ /bin/echo "Hello World"
  Hello World

If we specify the **--help** option to the commands, we can see that they respond
differently based on this input:

.. code-block:: bash
  :emphasize-lines: 1,3

  $ echo --help
  --help
  $ /bin/echo --help
  Usage: /bin/echo [SHORT-OPTION]... [STRING]...
  or:  /bin/echo LONG-OPTION
  Echo the STRING(s) to standard output.
  ... (output snipped) ...

.. index:: stdin, stdout, stderr, redirect

I/O Redirection
---------------

Any process on Linux that wants to interact with the world, will need to do this by
working with files or devices. Devices are presented as special files in the */dev*
filesystem in Linux, so for most if not all commands, working with devices is
indistinguishable from working with files.

As we have seen with all command upto this point, the output from various commands is
written back to the screen, and if needed, input is usually read from the keyboard. These
are devices, but for the program these devices are just like files. Every file that is
read from or written to, will need to be accessed using a numbered *file-handle*.

Each process in UNIX and Linux will have multiple *file-handles*, and there are three
file-handles that every process will always have initially, these correspond with *STDIN*
(file-handle 0), *STDOUT* (file-handle 1), and *STDERR* (file-handle 2).

  * STDIN is the input that the command processes. By default this will be data coming in
    from the keyboard, for example, when running the **read** command, input will be read
    from the user entering text on the keyboard.
  * STDOUT is the default output location, which will normally be sent to the screen, for
    example when entering the **date** command, it will print the current date and time
    back to the screen.
  * Lastly, STDERR is the default error location, this is where programs will send their
    error and warning messages. Normally these will also end up on the screen, mixed with
    other output from STDOUT.

These 3 default file-handles can also be accessed from the filesystem as */dev/stdin*,
*/dev/stdout* and */dev/stderr*.

=========== ======= ==========  ===============
Device      Handle  Default     How to redirect
=========== ======= ==========  ===============
/dev/stdin  0       Keyboard    < or 0<
/dev/stdout 1       Screen      > or 1>
/dev/stderr 2       Screen           2>
=========== ======= ==========  ===============

Using the shell we can run programs, but we can also decide what they should use and input
and output. If we change these inputs or outputs from the default settings, we call I/O
redirection. We can redirect input, output or stderr using the redirects in the table
above.

.. index:: >

For example, we can run a command, and redirect the output to a file

.. code-block:: bash
  :emphasize-lines: 1,3,4

  $ date
  Sat Nov 24 23:32:58 CET 2018
  $ date > /tmp/currenttime
  $ cat /tmp/currenttime
  Sat Nov 24 23:33:21 CET 2018

As you can see, on line 1, we run the **date** command without any output-redirection, so
the output is sent to it's default location, which is /dev/stdout, which results in output
being written to the shell-session we are currently in. In the second command, we redirect
the output to the file */tmp/currenttime* and notice that there is no output back to the
shell, we are just greeted with a new prompt. If we then look at the file where we
redirected the ouput of the date command into, using **cat /tmp/currenttime**, we can see
that the output was placed here.

.. index:: >, >>

Using output-redirection with **>** (or 1> or 2> and sending output to a (possibly
existing) file, will cause this file to be overwritten, so if you run multiple commands
after eachother, redirecting the output to the same file over and over again, only tha
last added data will still be present in the file. To append to a file instead of
overwriting it, we can redirect with **>>**.

.. code-block:: bash
  :emphasize-lines: 1,3,4,5,6,8,9,10,11,16
  :linenos:

  $ echo 1
  1
  $ echo 1 > /tmp/output
  $ echo 2 > /tmp/output
  $ echo 3 > /tmp/output
  $ cat /tmp/output
  3
  $ echo 4 >> /tmp/output
  $ echo 5 >> /tmp/output
  $ echo 6 >> /tmp/output
  $ cat /tmp/output
  3
  4
  5
  6
  $ cat < /tmp/output
  3
  4
  5
  6

In the example above, we also see a version of input redirection, on line 16 the **cat**
command is used with it's input redirected from the /tmp/output file. This result is the
same as the usage of **cat** on line 11, but functionally, the version on line 16 uses
/dev/stdin, while the version on line 11 opens the file named on the commandline as a new
file-handle (number 3), and then proceeeds to read from there. 

.. index:: 2>, 2>&1

.. code-block:: bash
  :emphasize-lines: 1,2
  :linenos:

  $ find / > /tmp/find-stdout 2> /tmp/find-stderr
  $ find / > /tmp/find-both 2>&1
  $ ls -la /tmp/find-*
  -rw-rw-r--. 1 yourname yourname 12613282 Nov 24 23:57 /tmp/find-both
  -rw-rw-r--. 1 yourname yourname    71229 Nov 24 23:55 /tmp/find-stderr
  -rw-rw-r--. 1 yourname yourname 12550011 Nov 24 23:55 /tmp/find-stdout

In the example above, we see redirects for STDOUT and STDERR on line 1, and a new
redirection on line 2. With the **2>&1** notation we tell the shell to redirect STDERR
(2>) to the location of filehandle 1 (&1), so, send error-output to the same location we
are sending the normal output, and the normal output was redirected to /tmp/find-both, so
the error-output ends up in here as well. This can be verified by checking the file-sizes,
the filesize of find-both is (almost) the same as the other two combined.

.. index:: wc, pipe, |

Pipelines
---------

In the previous section we have seen I/O redirection used to send output from commands to
a file, but with pipes we can take the output from a single command and hand it over as
the input to the next command, in essence coupling the two commands together in a
pipeline. In the example below, we send the output of the **ls** command to the input of
the **wc** command. This command counts the number of characters, words and lines in it's
input, and returns these to the output:

.. code-block:: bash
  :emphasize-lines: 1

  $ ls | wc 
  18      18     571

Using pipes (**|**), we can connect many simple commands together to perform difficult
tasks, pipes are a very strong feature and part of the UNIX philosophy. As we have not
discussed many of the available commands that Linux provides, this might not be clear to
you yet, but as you learn to use the various text-processing commands Linux has to offer,
you can start building more advanced pipelines.

.. code-block:: bash
  :emphasize-lines: 1

  $ cat /etc/passwd | grep yourname | cut -d: -f 6 | rev
  emanruoy/emoh/

As the example above shows, 4 commands that are put in a single pipeline. We will look
into many of these commands in future chapters.
