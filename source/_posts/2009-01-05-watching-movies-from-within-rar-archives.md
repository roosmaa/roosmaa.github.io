---
title: Watching movies from within RAR archives
author: Mart
layout: post
permalink: /watching-movies-from-within-rar-archives/
categories:
  - Guides
tags:
  - Shell script
---
Occasionally it happens, that you have a big multimedia file in a RAR archive and not enough room on your disk drives to unpack it. Not a problem when using command line and pipes!

`$ unrar p -inul homeVid.part01.rar video.avi | mplayer -`

Time after time the power of command line just can&#8217;t cease to amaze me.