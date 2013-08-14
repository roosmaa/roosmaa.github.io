---
title: Extendable Android applications
author: Mart
excerpt: 'How to create extendable applications for Android and how to avoid the Apple AppStore LITE & full version mess. And what you should be looking out for.'
layout: post
permalink: /extendable-android-applications/
categories:
  - Guides
tags:
  - Android
  - Java
---
The iPhone AppStore is filled with LITE and full versions of the same applications and such practice has become quite common. However I wanted to find out what would be the best way to create an extendable application on the Android platform. I decided to port <a href="http://ieuromillions.alexandre-gomes.com/" target="_blank">iEuroMillions</a> iPhone app over to Android as Alexandre had asked me to do it ages ago.

I wanted to make it so that you had the free base version of the application and then an add-on that you could buy and get the added functionality in the original application. Also, the base version would advertise the extra functionality that was obtainable.

<div id="attachment_182" class="wp-caption aligncenter" style="width: 218px">
  <a href="http://www.roosmaa.net/wp-content/uploads/2009/12/ieuromillions_iphone.jpg"><img class="size-medium wp-image-182" title="iEuroMillions (iPhone)" src="http://www.roosmaa.net/wp-content/uploads/2009/12/ieuromillions_iphone-208x300.jpg" alt="" width="208" height="300" /></a><p class="wp-caption-text">
    iEuroMillions (iPhone)
  </p>
</div>

As can be seen from the image above, the iEuroMillions main navigational element is a tab-bar. Luckily for me Androids TabActivity (TabHost, TabWidget, etc) supports tab content in Intent/Activity form. This saved me a lot of headache as I could just use the activities in my extension package.

To do that however I had to make sure that both of my applications were using the same UID, because else the Activity embedding as the tab content would fail. Having set the android:sharedUserId attribute in my AndroidManifest.xml I was all set and I could easily use the Activities in my extension package.

<pre class="brush: java; title: ; notranslate" title="">tab = host.newTabSpec("play");
tab.setIndicator(res.getText(R.string.tab_play), res.getDrawable(R.drawable.play));
it = new Intent();
it.setComponent(new ComponentName(PKG_STATS, PKG_STATS+".PlayActivity"));
tab.setContent(it);
host.addTab(tab);
</pre>

In the code sample above you can also see how to send explicit intents to activities in separate packages. In order to add a dummy page where to advertise your extension you need to check if your extension is installed and if not change the intent in the tab creation code.

<pre class="brush: java; title: ; notranslate" title="">boolean hasPkg = false;
PackageManager pm = getPackageManager();
try
{
      pm.getPackageInfo(PKG_STATS, 0);
      hasPkg = true;
}
catch (NameNotFoundException e)
{}
</pre>

For iEuroMillions there is some network activity in the main package, and when the numbers are updated I had to notify every activity in the tabs that was interested. I found that the easiest and most elegant solution to do that is to broadcast an Intent and let the applications listen for it.

<div id="attachment_186" class="wp-caption aligncenter" style="width: 220px">
  <a href="http://www.roosmaa.net/wp-content/uploads/2009/12/buynow.png"><img class="size-medium wp-image-186" title="iEuroMillions (Android)" src="http://www.roosmaa.net/wp-content/uploads/2009/12/buynow-210x300.png" alt="" width="210" height="300" /></a><p class="wp-caption-text">
    iEuroMillions (Android)
  </p>
</div>

There are however 2 annoying bugs with this approach. First and the most annoying is that if the user should uninstall the extension package, then the INTERNET permission I use in the main package gets revoked, this is due to the shared user id. I couldn&#8217;t find a workaround for it, so I&#8217;m just hoping that the user doesn&#8217;t uninstall the extension without uninstalling/reinstalling the main package.

Second bug that I came across was with the content menu. I created the menu in the TabActivity by inflating the menu xml, as the good practice of Android dictates. However, whenever a tab with an activity from another process was open (the extension activity), Android would crash and burn. It turned out that somewhere in the internals Android tried to get the drawable for the menu icon, but somehow was using the extension context when it should have been using the base application context.

The workaround for that was quite simple, ditch the menu.xml and create the menus in code and load the drawable when creating the menu, not when displaying them.