.. MIT License
   Copyright © 2018 Sig-I/O Automatisering / Mark Janssen, Licensed under the MIT license

IP Networking
=============

In this chapter we will look into network configuration in Linux, as systems without
network connection are very rare in this day and age. Networking in Linux consists of a
few seperate parts:

* Network card drivers
* IP address configuration
* Routing
* Firewalls and security features

Network types
-------------

These days, almost all networks you will encounter will be :abbr:`IP (Internet Protocol)`
based networks, but Linux also has support for various other older and exotic networking
protocols. In this book we will only be talking about :abbr:`IP (Internet Protocol)`
networks, as this fits the scope for LPI and RHCSA certifications, and what you will use
daily.

Looking further at networking on modern systems, we can look at wired and wireless
networking, with wireless further split into cellular and WiFi-based networks. In general,
Linux supports almost all network-interfaces out of the box, as network-chip manufacturers
usually work with the Linux kernel community to get support for new devices built in
quickly. Only very occasionally you will encounter a network-card (mostly wifi-based) that
will not work under Linux out of the box.

Addressing
----------

The two commonly used network protocols currently are :abbr:`IPv4 (Internet Protocol
version 4)` and :abbr:`IPv6 (Internet Protocol version 6)`. IPv4 is the *original*
protocol of the internet, and practically all systems will have an IPv4 address.

IPv4 addresses are presented (for human use) as 4 decimal 8-bit numbers, seperated by
dots. An IPv4 address is 32-bits in total, which limits the number of unique addresses to
2^32, which is slightly over 4 billion.

Example IPv4 addresses

.. hlist::
   :columns: 4

   * 10.20.30.40
   * 172.16.17.18
   * 188.40.154.15
   * 255.255.255.255


Many systems will also have IPv6 addresses, as the number of available IPv4 addresses was
quickly running out. IPv6 addresses are 128-bits in size, and are printed as 8 groups of
4 hexadecimal digits seperated with semicolons.

Example IPv6 addresses

.. hlist::
   :columns: 2

   * 2001:0470:7f95:0000:4e6c:ec8e:0ba1:694b
   * 2a00:1450:400e:080c:0000:0000:0000:200e
   * fe80:0000:0000:0000:b0f6:2f66:af28:86a2
   * 2001:0888:0000:0018:0000:0000:0000:0080

.. sidebar:: Hexadecimal

  In the hexadecimal (16-numbered) counting system, we use the digits 0 through 9, and the
  letters 'a' through 'f'. Hexadecimal is used a lot with computer-systems, as it is a
  short notation which can be quickly converted to binary, which would be a lot more
  difficult to read correctly for humans. Every hexadecimal digit can represent 16 values,
  which map directly to 4 binary digits.

  .. tabularcolumns:: |L|L|R|
  .. csv-table:: Decimal, Hexadecimal and Binary
    :header: "Dec", "Hex", "Bin"

    0,0,0
    1,1,1
    2,2,10
    3,3,11
    4,4,100
    5,5,101
    6,6,110
    7,7,111
    8,8,1000
    9,9,1001
    10,A,1010
    11,B,1011
    12,C,1100
    13,D,1101
    14,E,1110
    15,F,1111


Since these addresses are quite long, a shorter notation is also allowed, as long as this
shorter notation can be converted back consitently. In this notation, leading zeros in
blocks can be removed, so:

2001:0888:0000:0018:0000:0000:0000:0080

would become:

2001:888:0:18:0:0:0:80

Furthermore, repeated blocks of only zero's can be removed and replaced with 2 colons.
However, this can only be done once, otherwise it wouldn't be clear how many zeros should
be inserted here. Using this method, the above address is further shortened to:

2001:888:0:18::80

This is the format as seen from the various Linux commands.



