---
title: Making Gtk scrollbars more touchscreen friendly
author: Mart
excerpt: "I recently acquired an old tablet PC which I planned to use as an ebook reader. Using my preferred PDF reader, which at the moment is Evince I soon discovered that it's quite complicated to scroll the document as you have to hit that little scrollbar on the right. Evince does support grab to scroll, but only with middle mouse button, but that is not a valid option with a touchscreen."
layout: post
permalink: /making-gtk-scrollbars-more-touchscreen-friendly/
categories:
  - Guides
tags:
  - GTK+
---
I recently acquired an old tablet PC which I planned to use as an ebook reader. Using my preferred PDF reader, which at the moment is Evince I soon discovered that it&#8217;s quite complicated to scroll the document as you have to hit that little scrollbar on the right. Evince does support grab to scroll, but only with middle mouse button, but that is not a valid option with a touchscreen.

One option would have been to recompile Evince and swap the mouse left and middle buttons, but I didn&#8217;t feel like going through the hassle. Instead I decided to solve my problem with a simple .gtkrc &#8220;hack&#8221; by making scroll bar buttons bigger. It took some experimenting, because I was expecting some style properties containing width and height, but they were nowhere to be found.

Here is the content of my ~/.gtkrc-2.0 file on my tablet, it only makes the Evince main window scrollbar bigger, but can be modified to make any scrollbar bigger:

{% codeblock %}{% raw %}
style "big-scrolls" {
GtkRange::stepper-size = 50
GtkRange::slider-width = 50
}

widget_class "EvWindow.*.GtkScrolledWindow.*" style "big-scrolls"
{% endraw %}{% endcodeblock %}

When constructing the selector path for the style, one can use <a href="http://chipx86.github.com/gtkparasite/" target="_blank">Parasite</a> to inspect any existing application. That&#8217;s at least what I did with Evince.