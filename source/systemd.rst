.. MIT License
   Copyright © 2018 Sig-I/O Automatisering / Mark Janssen, Licensed under the MIT license

Systemd
=======

Systemd is an important set ot tools and processes related to booting Linux, starting and
stopping of services, mounting filesystems, and various other system tasks. 

Most modern Linux systems have switched to using Systemd in recent years, this has
resulted in many distributions becoming a lot more like one another, where before there
were many variations in startup-, service- and resource-management. So most of this
chapter will be relevant for Debian versions 8 and higher, centos and redhat version 7 and
up, and ubuntu version 16.x

Systemd as pid-1
----------------

Unix systems usually start a single 'master' process, which takes care of starting other
processes and services. When this initial process stops, the system becomes unusable. This
process was generally named :file:`/sbin/init`. Systemd also performs the task of the
init-process, and you can find it running on any systemd-enabled system with process-id 1.

Systemd's tasks as init consist of starting processes, keeping track of these processes
and any child-processes started from these, killing processes and cleaning up after them,
and finally shutting down or restarting the system.

Systemd as Service Manager
--------------------------

Systemd is also a Service Manager, this means that it will make sure certain services are
running, and even restarting these when they fail or exit, or starting specific tasks when
an incoming network-connection is detected.

Systemd uses configuration files, named 'unit-files', which come in various variants. Each
type has it's own function. A unit file has a name, which can be chosed freely, and an
extension, which designates it's type. Possible extensions are:

=============   ===================================================
Unit file       Function
=============   ===================================================
.service        Determines how to start a specific service
.socket         Socket-based activation configuration
.mount          Configuration for mounts and filesystems
.timer          Timed or recurring jobs
.target         Systemd targets, a collection of tasks
.slice          A hierarchical resource limitation
=============   ===================================================

Systemd Targets
---------------

Systemd targets can be seen as a replacement for the runlevels that we had in previous
init-systems like sysv-init. They describe groups of services and processes that we want
to have available in a specific use-case.

Using the **systemctl isolate <target>** command, we can ask systemd to switch to a specific
target set of services.

The targets available to the current system can be listed using the **systemctl
list-units | grep target** command, and the default target can be queried or configured
using **systemctl get-default** and **systemctl set-default <target>** commands.

.. index::
   single: systemctl
   single: target
   single: systemctl; set-default
   single: systemctl; get-default
   single: systemctl; list-units
   single: systemctl; isolate
