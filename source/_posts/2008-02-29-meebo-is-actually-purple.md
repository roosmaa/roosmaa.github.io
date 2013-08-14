---
title: Meebo is actually purple
author: Mart
layout: post
permalink: /meebo-is-actually-purple/
syntaxhighlighter_encoded:
  - 1
categories:
  - Thoughts
tags:
  - Meebo
  - Pidgin
---
<a title="A popular web-based IM client" href="http://www.meebo.com/" target="_blank">Meebo</a> is a popular web-based multiprotocol instant messenger (IM) application and my one and only preference for accessing my buddy list when I&#8217;m not near my own computer or when I happen to be working in Windows. I had never ever thought of how did Meebo achieve the multiprotocol support it had, but I suppose I thought that they had done it all by them selves.

Today while surfing the <a href="http://www.pidgin.im/" target="_blank">Pidgin</a> (formerly Gaim) website, I came across <a title="A page introducing libpurple" href="http://developer.pidgin.im/wiki/WhatIsLibpurple" target="_blank">a page</a> that introduced libpurple. To my greatest surprise the list of applications using it contained Meebo as one of them. Given the fact that Meebo has utilised libpurple quite nicely, I couldn&#8217;t help but to wonder what other interesting applications could be built upon it.

<!--more-->

First interesting application that comes to mind is a IM bot that can provide some useful services. For instance a reminder application that could remind you of things you have asked to be reminded about.

Another approach would be to create some file (images, documents, etc) sharing bot for a group of people or some already existing online service. In the first case, the user would send the file to the bot and the other users of the bot would be notified of changes in hosted files, which they could in turn request from the bot. Or in the second case, the user would drop the file on the bot in their contact list and it would be automatically hosted on some site (<a href="http://www.imageshack.us/" target="_blank">ImageShack</a>, <a href="http://www.youtube.com/" target="_blank">YouTube</a>, etc).

For my own personal preference, I would like to create a logging proxy, so that I could run it on my server. It would stay logged in 24/7 and take messages for me. When I log in myself, those messages would be displayed to me. I know there is offline messaging support included in major protocols already, but that doesn&#8217;t mean Pidgin support them yet. Besides, such approach would allow me to add some extra features to it.

Creating such services from scratch would require very much work, because implementing each IM protocol by oneself is a big task. Waste of precious time doesn&#8217;t appeal to many developers, so such ideas are most likely dumped. I know I&#8217;ve put them aside. But with libpurple, they could be implemented quite easily (if I understand it all correctly), and I would sure like to write that logging proxy in the near future!