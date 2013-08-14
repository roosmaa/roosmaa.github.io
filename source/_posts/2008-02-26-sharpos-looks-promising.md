---
title: SharpOS looks promising
author: Mart
layout: post
permalink: /sharpos-looks-promising/
syntaxhighlighter_encoded:
  - 1
categories:
  - Thoughts
tags:
  - .NET
  - 'C#'
  - OS
  - SharpOS
---
Over a year has passed since the creation of <a title="SharpOS" href="http://en.wikipedia.org/wiki/SharpOS_(operating_system)" target="_blank">SharpOS</a> <a title="SharpOS Mailing List" href="https://lists.sourceforge.net/lists/listinfo/sharpos-developers" target="_blank">mailing list</a>. I&#8217;ve not kept track with the messages there and the state of SharpOS for some time, but yesterday I got the time to have a peek at what has changed, and to my great surprise the project has been making nice progress.

> The SharpOS Project is a community effort to write an operating system based on .NET technology, with a strong sense of security and manageability.

<!--more-->The most interesting part in the project at the moment is their AOT (Ahead of Time) compiler, which generates native machine code. Why this is exiting, especially for me, is because I&#8217;ve had a similar idea for a year or two, but as it has seemed too big for me to take it on all by myself, it hasn&#8217;t moved any further than the basic brainstorming notes.

What is grand about this piece of software is that it&#8217;s written wholly in C#, which stretches the limits of the actual .NET Framework and puts it to situations that most of us wouldn&#8217;t even have dared to think of putting it. The fact that they already have achieved bootability is a great feat in my eyes, because converting IL (Intermediate Language) to actual machine code that the computer is able to execute is a big accomplishment.

As with every new OS, there is a clean page from where developers can start designing and writing code that does things differently, most of the times better, than its current equivalent. The main reason why such changes can&#8217;t be done in everyday systems is because of the sheer amount of code that should be updated. Because SharpOS has a lot of potential in my eyes (it could very well be the next big thing is the OS world), the new and improved ways of doing things could be adopted and eventually be the one and only way of doing things.

I myself plan to give my helping hand to the project when I get more free time in about 6 months or so. I would probably help them with the GUI side of the project, because, yet again, it overlaps with one idea of mine I&#8217;ve got stashed away somewhere.