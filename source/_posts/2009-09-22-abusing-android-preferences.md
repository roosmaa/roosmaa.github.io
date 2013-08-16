---
title: Abusing Android Preferences
author: Mart
excerpt: "Sometimes a situation arises when you want to use a certain Preference, but also want to include some business logic to map the values to different types/values depending on certain conditions. Reimplementing those controls isn't very productive most of the times. But there is a way..."
layout: post
permalink: /abusing-android-preferences/
categories:
  - Guides
tags:
  - Android
  - Java
---
Sometimes a situation arises when you want to use a certain <a href="http://developer.android.com/reference/android/preference/Preference.html" target="_blank">Preference</a>, but also want to include some business logic to map the values to different types/values depending on certain conditions. <a href="http://developer.android.com/reference/android/preference/ListPreference.html" target="_blank">ListPreference</a> for instance doesn&#8217;t provide any way to neatly have the entries generated dynamically or saving them to preferences other than string ones.

Reimplementing those controls isn&#8217;t very productive most of the times. There is a way to get things done quickly, but in my opinion it isn&#8217;t the cleanest of solutions. The following code shows how to re-route <a href="http://developer.android.com/reference/android/preference/ListPreference.html" target="_blank">ListPreferences</a> values to a boolean preference:

{% codeblock lang:java %}{% raw %}
public class MyPreference extends ListPreference
{
  // ...
  @Override
  protected String getPersistedString(String defaultReturnValue)
  {
    boolean val = super.getPersistedBoolean(defaultReturnValue.equalsIgnoreCase("true"));
    return val ? "true" : "false";
  }

  @Override
  protected boolean persistString(String value)
  {
    boolean val = value.equalsIgnoreCase("true");
    return super.persistBoolean(val);
  }
}
{% endraw %}{% endcodeblock %}
