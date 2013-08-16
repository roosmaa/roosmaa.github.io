---
title: Drawing beautiful bezier lines
author: Mart
excerpt: At first glance drawing attractive bezier lines might be a bit taunting, but in reality there is nothing easier...
layout: post
permalink: /drawing-beautiful-bezier-lines/
categories:
  - Guides
tags:
  - Android
  - Java
---

{% img right /images/content/2009/09/bezier.png 159 198 'Bezier lines' %}

At first glance drawing attractive bezier lines might be a bit taunting, but in reality there is nothing easier. The trick is using 2 colors to draw the lines. The chosen color for the line and a darker (depending on the overall background) version of it.

Then you need to set up the <a href="http://developer.android.com/reference/android/graphics/Paint.html" target="_blank">Paint</a> objects, preferably not in the onDraw method as it would create more work for the GC. One Paint object will be used for drawing the border, the other for the line itself.

For example some good defaults for Paint objects are:  

{% codeblock lang:java %}{% raw %}
pLine = new Paint() {{
  setStyle(Paint.Style.STROKE);
  setAntiAlias(true);
  setStrokeWidth(1.5f);
  setColor(...); // Line color
}};

pLineBorder = new Paint() {{
  setStyle(Paint.Style.STROKE);
  setAntiAlias(true);
  setStrokeWidth(3.0f);
  setStrokeCap(Cap.ROUND);
  setColor(...); // Darker version of the color
}};
{% endraw %}{% endcodeblock %}

Then the actual drawing of the lines in the <a href="http://developer.android.com/reference/android/view/View.html#onDraw(android.graphics.Canvas)" target="_blank">onDraw</a> method would look something like this:

{% codeblock lang:java %}{% raw %}
Path p = new Path();
Point mid = new Point();
// ...
Point start = ...;
Point end = ...;
mid.set((start.x + end.x) / 2, (start.y + end.y) / 2);

// Draw line connecting the two points:
p.reset();
p.moveTo(start.x, start.y);
p.quadTo((start.x + mid.x) / 2, start.y, mid.x, mid.y);
p.quadTo((mid.x + end.x) / 2, end.y, end.x, end.y);

canvas.drawPath(p, pLineBorder);
canvas.drawPath(p, pLine);
{% endraw %}{% endcodeblock %}

And presto! You&#8217;ve got yourself a nice bezier line.