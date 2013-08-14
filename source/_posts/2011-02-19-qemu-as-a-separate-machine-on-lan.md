---
title: QEMU as a separate machine on LAN
author: Mart
excerpt: 'With QEMU you have basically two choices for networking: usermode and TAP/TUN. For running servers on the Guest OS the TAP/TUN is the way to go. Simple way to set-up the bridged networking and TAP interfaces for QEMU.'
layout: post
permalink: /qemu-as-a-separate-machine-on-lan/
categories:
  - Guides
tags:
  - Linux
  - QEMU
  - Shell script
---
With QEMU you basically got 2 choices for networking:

*   Usermode networking, where QEMU does some magic and you can access internet from the Guest OS and redirect ports to the Guest OS,
*   TUN/TAP interface way which gives the Guest OS a separate IP on your LAN.

Usermode is not very good for running servers on them, so TUN/TAP it is. The problem with TUN/TAP is that on the internet there are many posts explaining it, but too many of them make it annoyingly hard or even worse, go about it the wrong way.

So, the easy and nice way: first you need to set-up your network connection as a bridged one. To do that globally we need to edit the */etc/network/interfaces* file and make it look like this:

<pre class="brush: plain; title: ; notranslate" title="">auto lo
iface lo inet loopback

iface eth0 inet manual

auto br0
iface br0 inet dhcp
	pre-up ifconfig eth0 down
	pre-up brctl addbr br0
	pre-up brctl addif br0 eth0
	pre-up ifconfig eth0 up
	post-down ifconfig eth0 down
	post-down brctl delif br0 eth0
	post-down brctl delbr br0
</pre>

You probably want to reboot your computer for the changes to take effect.

Now the default */etc/qemu-ifup* on Ubuntu looks like this:

<pre class="brush: bash; title: ; notranslate" title="">#!/bin/sh

switch=$(/sbin/ip route list | awk '/^default / { print $5 }')
/sbin/ifconfig $1 0.0.0.0 up
/usr/sbin/brctl addif ${switch} $1
</pre>

&#8230; and */etc/qemu-ifdown* looks like:

<pre class="brush: bash; title: ; notranslate" title="">#!/bin/sh

# NOTE: This script is intended to run in conjunction with qemu-ifup
#       which uses the same logic to find your bridge/switch

switch=$(/sbin/ip route list | awk '/^default / { print $5 }')

/usr/sbin/brctl delif $switch $1
/sbin/ifconfig $1 0.0.0.0 down
</pre>

QEMU executes those scripts automatically, however, they expect to be run as root, which I don&#8217;t like, so we will have to execute them manually with sudo if we want to run qemu as a normal user. A simple QEMU wrapper that I use looks like:

<pre class="brush: bash; title: ; notranslate" title="">#!/bin/sh

IFACE=$(sudo tunctl -b -u $(whoami))
sudo /etc/qemu-ifup $IFACE

qemu -net nic -net tap,ifname=$IFACE,script=no $@

sudo /etc/qemu-ifdown $IFACE
sudo tunctl -d $IFACE
</pre>

This creates a new TAP interface, runs the qemu-ifup script to add it to the default bridge (br0) you should be using, runs QEMU, and afterwards cleans up after itself. And for me this is much more elegant solution compared to most out there.