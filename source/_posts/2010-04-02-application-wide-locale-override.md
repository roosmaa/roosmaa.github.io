---
title: Application wide locale override
author: Mart
excerpt: How to override the system wide locale in your application and get away with it. Demonstrates an application wide approach where there is no need to the change the existing activities.
layout: post
permalink: /application-wide-locale-override/
categories:
  - Guides
tags:
  - Android
  - I18n
  - Java
---
There are quite many blog posts out there on how to override the system locale in an Android application (like <a title="Force Localize an Application on Android" href="http://www.tutorialforandroid.com/2009/01/force-localize-application-on-android.html" target="_blank">this</a> or <a title="Force Locale on Android" href="http://adrianvintu.com/blogengine/post/Force-Locale-on-Android.aspx" target="_blank">that</a>). However they all are activity specific and to my disliking propagate the usage of Locale.setDefault(), which in my opinion should not be touched and in practice does nothing to help us achieve our goals.

As mentioned before I didn&#8217;t like the idea of having to copy the same code into all of my activities to customize the locale and wanted to do it application wide. The overriding of the <a href="http://developer.android.com/reference/android/app/Application.html" target="_blank">Application</a> is not a well known technique but it should be for cases like this one. Here is an example how I achieved the application wide locale overriding without altering any of my activities:

<pre class="brush: java; title: ; notranslate" title="">public class MyApplication extends Application
{
  @Override
  public void onCreate()
  {
    updateLanguage(this);
    super.onCreate();
  }

  public static void updateLanguage(Context ctx)
  {
    SharedPreferences prefs = PreferenceManager.getDefaultSharedPreferences(ctx);
    String lang = prefs.getString("locale_override", "");
    updateLanguage(ctx, lang);
  }

  public static void updateLanguage(Context ctx, String lang)
  {
    Configuration cfg = new Configuration();
    if (!TextUtils.isEmpty(lang))
      cfg.locale = new Locale(lang);
    else
      cfg.locale = Locale.getDefault();

    ctx.getResources().updateConfiguration(cfg, null);
  }
}
</pre>

On a little side note, the most common problem with the other locale overriding examples out there was that on Android >2.0 the locale change triggered an incorrect scaling of items. A workaround was by adjusting the *android:configChanges* property for each of the activities. However this ugly situation can be avoided by just passing null as the 2nd argument to the <a href="http://developer.android.com/reference/android/content/res/Resources.html#updateConfiguration(android.content.res.Configuration, android.util.DisplayMetrics)" target="_blank">Resources.updateConfiguration()</a> function.

Back to the application wide approach. To tell Android to use your class for the Application instance, you need to specify it in the AndroidManifest.xml like this:

<pre class="brush: xml; title: ; notranslate" title="">&lt;application android:name="com.example.MyApplication" ...&gt;
	...
&lt;/application&gt;
</pre>

As you can see from the code, if you want to override the locale in your application you just store the locale in the *locale_override* preference, if you want to use the system default locale, you unset it or just set it to an empty string.

If you want to update the locale temporarily for the duration of the application lifespan you just call the MyApplication.updateLanguage(getApplicationContext(), &#8220;en&#8221;) function with the language you want to switch to.

The main problem with this approach as with the others out there is that whenever you change the locale at runtime, the activities already created with a different locale will have UI&#8217;s with the old locale. This can be worked around in many cases, but sometimes it&#8217;s just easier to tell the user to restart the application before the changes take affect fully.

Also it is worth noting that Services get their own MyApplication instances, or at least the change of the locale doesn&#8217;t propagate the existing Services as it does for existing Activities, so whenever you need localized resources in the Service you are better off calling the updateLanguage(getApplicationContext()) before fetching the resources.

<div id="_mcePaste" style="overflow: hidden; position: absolute; left: -10000px; top: 551px; width: 1px; height: 1px;">
  <pre>Context ctx, String lang)</pre>
</div>