.. MIT License
   Copyright © 2018 Sig-I/O Automatisering / Mark Janssen, Licensed under the MIT license

User Management
===============

The Linux permission system is based on the concept of users and groups. Every file on the
system is owned by a user and group combination, every running process runs with a specific
user and group permission.

.. index:: /etc/passwd

The /etc/passwd file
----------------------

Local useraccounts are stored in the :file:`/etc/passwd` file. This is the traditional
password file for UNIX and Linux accounts. The :file:`/etc/passwd` file doesn't actually
contain the (encrypted) passwords anymore these days, as these are stored in
:file:`/etc/shadow`, but passwords were stored here originally, and you could actually
still put passwords in the :file:`/etc/passwd` file, though this is highly discouraged, as
this file is world-readably, and this would give everyone access to the encrypted
passwords.

The :file:`/etc/passwd` file consists of various fields, seperated by semicolons (*:*),
with one user per line. The file-format is documented in the *passwd(5)* manual-page, and
consists of the following fields (in this order):

* login name: The username for the user, which should be lowercase, and preferably not too
  long.

* password: This field used to hold the password originally, these days the field will
  either contain an asterisk (*) or the letter 'x'. The letter 'x' tells the system that
  the password for this user should be read from the :file:`/etc/shadow` file. The '*'
  character is used to disable password-authentication for this account.

* uid: The user identification number, a (preferably) unique number that denotes this
  user. For normal users, this number should be above the value of UID_MIN, which is
  normally *1000*. The UID_MIN, UID_MAX (and other) values can be found in the
  :file:`/etc/login.defs` file.

* gid: The primary group number for the user, which referenced the group entry from the
  :file:`/etc/group` file, which we will discuss below.

* :abbr:`gecos (General Electric Comprehensive Operating System)`: The GECOS (or comment)
  field is used to put human-readable information about the user, usually at least the
  full name and somethings things like phone- and room-numbers. The information in this
  field will be used by the **finger(1)** command.

* directory: The home-directory for the user, the initial directory where the user is
  placed after logging in, and where the system will look for various configuration-files
  for this user. This will also be the location the :envvar:`HOME` variable points to for
  this user.

* shell: Which shell should be started when the user logs in. If no shell is specified,
  :file:`/bin/sh` will be started. If this field is set to a not-existing filename, the
  user will not be able to login, but to specifically enforce this, :file:`/sbin/nologin`
  is used in general, though you might also find :file:`/bin/false` used for this.

During the installation, at least one useraccount will usually be created, we'll use this
entry as the example here:

.. code-block:: none
  :caption: A password-file entry
  :name: passwd-entry

  yourname:x:1000:1000:Your Name:/home/yourname:/bin/bash

.. index:: /etc/shadow, shadow

The /etc/shadow file
----------------------

The :file:`/etc/shadow` file contains the fields related to user-accounts that do not need
to publicly readable. This is primarily the password, but it also contains fields related
to account and password expiration.

The basic format of the :file:`/etc/shadow` file is the same as the :file:`/etc/passwd`
file, a line of semi-colon seperated fields:

* login name: The username for the user, the same as in :file:`/etc/passwd`

.. sidebar:: Hashing

  Passwords are hashed. A hash function is any function that can be used to map data of
  arbitrary size to data of a fixed size. They are also useful in cryptography. A cryptographic hash
  function allows one to easily verify that some input data maps to a given hash value,
  but if the input data is unknown, it is deliberately difficult to reconstruct it (or any
  equivalent alternatives) by knowing the stored hash value.

  In plain english, this means that an input will be transferred to a fixed length output,
  which has no clear and simple way which can be decoded back in the original input. So a
  Linux system doesn't actually know you password, but it re-hashes the password you
  enter and compares this to the stored hash. If they match, your password was correct, if
  they don't match, then login is rejected.

* password: The encrypted password for the user.

* date of last password change: When the user has changed their password for the last
  time, expressed in days since Jan 1 1970.

* minimum password age: The minimum age a password must have before a user is allowed to
  change it, represented in days. A value of *0* means that there is no minimum, which is
  also a recommended value, as users should always be able to change a password,
  especially when it has been compromised.

* maximum password age: How long a password can be used before it must be changed,
  specified in number of days. A zero in this field means the password doesn't expire.
  Setting this value to something lower than the minimum password age will deny
  password-changes by the user.

* password warning period: The number of days before password-expiration that the user
  will get a warning printed when they login.

* password inactivity period: This specifies the amount of days after password-expiration
  that a user can still login with their old password, but will be forced to change their
  password before they are actually logged in. After this period a login attempt will be
  denied, even if the password would have been correct.

* reserved: There is one reserved field, which has no currently planned function.

.. code-block:: none
  :caption: An entry from /etc/shadow
  :name: shadow-entry

  yourname:$6$ls6.d7Yg$0g8jRJnHRWIcLwaitgI....aXixdPUB4YjNLg1bc.C/:17862:0:99999:7:::

.. index:: password, hash, md5, sha-256, sha-512

The password field in /etc/shadow
---------------------------------

The password for a useraccount is stored, in a hashed form, in the password field of the
:file:`/etc/shadow` file. This field consists of 3 parts, prefixed by dollar-symbols ($).
These fields are *id, *salt* and *hash*.

* id: This tells the system which password hash or encryption-method is used. Commonly
  used values are:

  * 1, for MD5 hashes
  * 5, for SHA-256 hashes
  * 6, for SHA-512 hashes

* salt: A salt-value, which is a randomly generated string used to add to the password
  before it is hashed. This is done to prevent make it harder to pre-compute password-hash
  dictionaries used in password-cracking.

* hash: The hashed version of the password and the salt value, hashed with the algorithm
  specified in the 'id' field.

The example that was shown above therefor used a SHA-512 hash (type 6), with a salt of
*ls6.d7Yg*.

.. index:: useradd

Adding a user-account
---------------------

You can create new useraccounts and change settings on existing users  with the
**useradd** command. The minimal invocation of **useradd** is just a simple: 

.. code-block:: bash
  :emphasize-lines: 1,3,4
  :caption: Creating a user

  # id keylee
  id: keylee: no such user
  # useradd keylee
  # id keylee
  uid=1001(keylee) gid=1001(keylee) groups=1001(keylee)

When you create a new useraccount, and do not specify and user-id for this user, the next
free number will be used. In this case, there was only a user with uid 1000, so the new
account will be created with uid 1001.

You can specify some options to **useradd** to modify the settings used for creating the
new account:

* **-c** specifies the contents of the comment / GECOS field
* **-d** specified the home-directory to use, normally this will be /home/<username>
* **-e YYYY-MM-DD**, specified an expiration-date for the account
* **-u** uid-number, use this specific UID instead of the next free number

There are many more options to specify, which you can find the the manual-page for
**useradd**, but these are the most-used flags.

.. index:: getent

To create a useraccount with some more settings filled in:

.. code-block:: bash
  :emphasize-lines: 1,2
  :caption: Creating a user

  # useradd -c "Malcolm Reynolds" -e 2018-12-31 -m -s /bin/bash -u 1020 -d /home/mal mal
  # getent passwd mal
  mal:x:1020:1020:Malcolm Reynolds:/home/mal:/bin/bash

Here we use the **getent** command to lookup account-information. Earlier we used **id**,
which only gave us the names and id-numbers for the user and groups. Getent is a generic
lookup function, which can be used to lookup users, hosts, networks, network-services and
many other things. Useraccount information can be found by doing **getent passwd**, as
seen here. This returns the line from the :file:`/etc/passwd` file matching our query.

.. seealso:: man getent(1), man useradd(8)

.. index:: /etc/group, /etc/gshadow, groups

The /etc/group file
-------------------

The counterpart of :file:`/etc/passwd` is :file:`/etc/group`. This file is used to
store the group-membership of all the users on the system in a format like the other
account database files.

:file:`/etc/group` contains 4 fields:

* group name: The name of the group, usually in lowercase and preferably without spaces.
* group password: Like in the case of :file:`/etc/passwd`, this will usually contain an
  'x' to signify the (optional) password is stored in a shadow file. In the case of groups
  this is :file:`/etc/gshadow`.
* group id: a numeric id used to uniquely identify this group.
* group members: a comma-seperated list of users who are a member of this group.

An example :file:`/etc/group` entry could look like this:

.. code-block:: none
  :caption: Example /etc/group entry

  firefly:x:200:mal,zoe,wash,inara,jayne,kaylee,simon,river,book

Users in Linux will have a primary group, this is the group identified by the *gid* field
in their :file:`/etc/passwd` entry. If they are a member of any other groups, their name
will be listed in the *group members* field in :file:`/etc/group`. In general, the primary
group a user is in will not have their name listed in the *group members* field.

As we have seen, the :file:`/etc/group` file has a password-field, which is used to ask
for a password when a user changes groups. In general this feature isn't used much if at
all, but support for this is still present in all Linux distributions.


.. index:: groupadd

Adding a group
--------------

Like adding a user with useradd, groups can be created with the **groupadd** command:

.. code-block:: none
  :caption: Groupadd usage

  # groupadd --help
  Usage: groupadd [options] GROUP

  Options:
    -f, --force                   exit successfully if the group already exists,
                                  and cancel -g if the GID is already used
    -g, --gid GID                 use GID for the new group
    -h, --help                    display this help message and exit
    -K, --key KEY=VALUE           override /etc/login.defs defaults
    -o, --non-unique              allow to create groups with duplicate
                                  (non-unique) GID
    -p, --password PASSWORD       use this encrypted password for the new group
    -r, --system                  create a system account
    -R, --root CHROOT_DIR         directory to chroot into

  # groupadd -g 200 firefly
  # getent group firefly
  firefly:x:200:


