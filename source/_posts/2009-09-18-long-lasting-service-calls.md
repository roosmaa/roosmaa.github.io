---
title: Long lasting service calls
author: Mart
excerpt: When developing for Android it is recommended that service calls should not block the thread and should create a new thread if they need to do time consuming tasks. I agree that blocking the UI thread is bad, but sometimes it can get ugly.
layout: post
permalink: /long-lasting-service-calls/
syntaxhighlighter_encoded:
  - 1
categories:
  - Thoughts
tags:
  - Android
  - Java
---
When developing for Android it is recommended that service calls should not block the thread and should create a new thread if they need to do time consuming tasks.

While I do agree that blocking the UI thread is bad I don&#8217;t think that the new thread should always happen at the service side.

When creating a thread on service side you need to somehow return the data afterwards. Having callbacks to do that is the only possibility, but it in some cases it adds an unnecessary amount of boilerplate code.

For example, say we are writing a <a href="http://www.reddit.com/" target="_blank">reddit</a> client. We have a service method to download and parse the data. When called the service spawns a new thread and returns from the function immediately.  
After finishing the working thread it looks up the callback (which we have assigned before the main service call) and sends the data to it.

In theory it sounds pretty good, but in practice it involves too much code and ugliness.  
This approach does justify itself when there are a lot of callbacks that can happen during the service method call/s, but for a situation like this it is quite troublesome to do it this way.

Instead, it is much easier to just do the work in the service method and blocking until the results are available and then return them.

On the caller side Android has a nice elegant <a href="http://developer.android.com/reference/android/os/AsyncTask.html" target="_blank">AsyncTask</a> class into which you can wrap the method call. And when it finally returns you are automatically in the UI thread, no need to mess with View.post()&#8217;ing and anonymous types.