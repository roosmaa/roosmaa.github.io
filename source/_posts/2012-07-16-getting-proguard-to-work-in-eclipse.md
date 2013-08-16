---
title: Getting ProGuard to work in Eclipse
author: Mart
layout: post
permalink: /getting-proguard-to-work-in-eclipse/
categories:
  - Guides
tags:
  - Android
  - Eclipse
  - Java
  - ProGuard
---
Up until yesterday, I thought that <a href="http://developer.android.com/tools/help/proguard.html" target="_blank">enabling ProGuard</a> for an Android project would be just adding one or two lines somewhere. I was kind of right — it was adding a single line to enable it, and a day of Googling and experimenting to get it actually working.

I wasn&#8217;t using the latest SDK tools when I started, but while fiddling everything I upgraded to *Android SDK Tools rev 20* and *Android SDK Platform-tools rev 12*, so the following applies to those versions, but might work for earlier or future versions as well.

During the trial and error process I encountered several different errors, some of them being more common than others.

The first problem was easy to find a solution on the web; ProGuard was throwing out warnings about the support-v4 library. Those just had to be disabled by adding an extra line to proguard.cfg:

{% codeblock %}{% raw %}
-dontwarn android.support.**
{% endraw %}{% endcodeblock %}

After which there was my first encounter with the dreaded  &#8221;Conversion to Dalvik format failed with error 1&#8243; failed. This kept appearing and disappearing a lot. And as there is absolutely no logs as to why it happened my only guess is that ProGuard failed to produce Dalvik compatible bytecode — which in turn means something is wrong somewhere.

There are many-many alleged solutions to this problem on Stackoverflow, but most of them had little or no impact on solving the problem. However, two of them combined worked for me.

First, you need to double-check your build paths (Project Properties → Java Build Path) and under the Source tab remove all *<library\_project>\_src* folders if you have any; and under Libraries tab you want to remove everything except the *Android x.x*. After having done that you need to re-add the library dependencies by invoking Android Tools → Fix Project Properties.

Second, there appears to be an annoying bug in the actual ADT themselves that mess with ProGuard somehow. What you need to do is disable automatic building in Eclipse (Project → Build Automatically), then clean your entire project and then rebuild it (Project → Build All). Credits for this step go to<a href="http://stackoverflow.com/users/576267/regex-rookie" target="_blank"> Regex Rookie</a> on <a href="http://stackoverflow.com/a/11008325/188083" target="_blank">Stack Overflow</a>.

There was a third problem as well, some IllegalArgumentException in ProGuard, saying that some method in the compatibility library was using something from SDK v14 and it couldn&#8217;t find it.

{% codeblock %}{% raw %}
Unexpected error while evaluating instruction:
  Class       = [android/support/v4/view/AccessibilityDelegateCompat$AccessibilityDelegateJellyBeanImpl]
  Method      = [newAccessiblityDelegateBridge(Landroid/support/v4/view/AccessibilityDelegateCompat;)Ljava/lang/Object;]
  Instruction = [18] areturn
  Exception   = 1 (Can't find any super classes of [android/support/v4/view/AccessibilityDelegateCompatIcs$1] (not even immediate super class [android/view/View$AccessibilityDelegate]))
Unexpected error while performing partial evaluation:
  Class       = [android/support/v4/view/AccessibilityDelegateCompat$AccessibilityDelegateJellyBeanImpl]
  Method      = [newAccessiblityDelegateBridge(Landroid/support/v4/view/AccessibilityDelegateCompat;)Ljava/lang/Object;]
  Exception   = 1 (Can't find any super classes of [android/support/v4/view/AccessibilityDelegateCompatIcs$1] (not even immediate super class [android/view/View$AccessibilityDelegate]))
java.lang.IllegalArgumentException: Can't find any super classes of [android/support/v4/view/AccessibilityDelegateCompatIcs$1] (not even immediate super class [android/view/View$AccessibilityDelegate])
	at proguard.evaluation.value.ReferenceValue.generalize(ReferenceValue.java:287)
	...
{% endraw %}{% endcodeblock %}

Solution for that was simple: bump up the target SDK version of the project to the latest one. Only thing to watch out with this one is that backwards compatibility stuff won&#8217;t be used when you run your app on newer platforms now &#8211; so you need to know what has changed in the platform and take that into consideration (ie with v14 the default AsyncTask behaviour changed).

And to recap, the check list for getting ProGuard to work:

1.  If using support library, bump up your target SDK to latest and add -dontwarn into proguard.cfg
2.  Check your build paths for rogue entries
3.  Disable automatic building, clean everything and rebuild

Hope this saves someone a couple of hours trying to figure this out.