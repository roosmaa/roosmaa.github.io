---
title: Usage pattern for dynamically registered BroadcastReceivers
author: Mart
excerpt: Dealing with dynamically registered BroadcastReceivers is tricky. This pattern makes it a bit easier.
layout: post
permalink: /usage-pattern-for-dynamically-registered-broadcastreceivers/
categories:
  - Guides
tags:
  - Android
  - Java
---
When registering BroadcastReceivers dynamically in your application, you need to keep track of them and unregister them. And when unregistering you need to make sure that you haven&#8217;t done it before, else you&#8217;ll get an IllegalArgumentException. Another annoyance is when you need to check if you actually registered the receiver already or not.

The following code sample illustrates a nice way to keep track of registered receivers and only allow deleting them once.

{% gist 3045908 %}
