---
title: Pacman making it explicit
author: Mart
excerpt: After getting hibernation to work, I accidentally rebooted to a previous state that had been left in the swap space. It pretty much trashed my filesystem. So, I wanted to reinstall all packages and somehow managed to reinstall them all as dependencies.
layout: post
permalink: /pacman-making-it-explicit/
categories:
  - Guides
tags:
  - Arch Linux
  - Pacman
  - Shell script
---
After getting hibernation to work, I accidentally rebooted to a previous state that had been left in the swap space. It pretty much trashed my filesystem. So, I wanted to reinstall all packages and somehow managed to reinstall them all as dependencies. To restore explicitly installed ones there should be a possibility to run `# pacman -S --asexplicit <PACKAGE>`, but for some reason it isn&#8217;t implemented in pacman v3.1.3.

So, I whipped up a quick hackish shell script to accomplish the task. Maybe someone with a similar problem will find this useful. But be warned, if something goes wrong, don&#8217;t blame me.

<pre class="brush: bash; title: ; notranslate" title="">#!/bin/bash
# File name: pkg_explicit

if [ -z &quot;$1&quot; ]; then echo &quot;Usage: $0 [--restore] &lt;PACKAGE&gt;&quot;; exit 1; fi

RESTORE=0
PKG_NAME=$1

if [ $1 == &quot;--restore&quot; ]; then
RESTORE=1
PKG_NAME=$2
fi

PKG=$(pacman -Q $PKG_NAME 2&gt; /dev/null)

if [ -z &quot;$PKG&quot; ]; then echo &quot;No suck package installed!&quot;; exit 1; fi

PKG_DIR=/var/lib/pacman/local/$(echo $PKG | sed -e &quot;s/ /-/&quot;)/

if [ ! -d $PKG_DIR ]; then echo &quot;No such package installed!&quot;; exit 1; fi

if [ $RESTORE -eq 0 ]; then
if [ -z &quot;$(pacman -Qd $PKG_NAME 2&gt; /dev/null)&quot; ]; then echo &quot;Package already explicit!&quot;; exit 1; fi
cp $PKG_DIR/desc{,.rec}
sed -i &quot;/%REASON%/,+2d&quot; $PKG_DIR/desc
else
if [ ! -e $PKG_DIR/desc.rec ]; then echo &quot;Nothing to restore!&quot;; exit 1; fi
mv $PKG_DIR/desc{.rec,}
fi
</pre>