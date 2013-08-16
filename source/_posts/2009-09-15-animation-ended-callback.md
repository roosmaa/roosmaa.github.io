---
title: Animation ended callback
author: Mart
excerpt: "The Android documentation gives off the impression that the only proper way to chain animations is by calculating their durations and then starting the next animation when the first should've been finished. While it works fine for animations sets defined in xml, it does break down when wanting to trigger some action in code right after the animation ended."
layout: post
permalink: /animation-ended-callback/
categories:
  - Guides
tags:
  - Android
  - Animations
  - Java
---
The Android documentation gives off the impression that the only proper way to chain animations is by calculating their durations and then starting the next animation when the first should&#8217;ve been finished. While it works fine for animations sets defined in xml, it does break down when wanting to trigger some action in code right after the animation ended.

When using the conventional way of calculating the end time one could assume that the same is true for triggers that have to happen in code. Say by calling View.postDelayed(..) . This works more or less, but when the delayed runnable is called it isn&#8217;t guaranteed that the animation has ended, it might be still running, or worse it ended ages ago and the user just watched a static screen without understanding if he should do something or has the program crashed.

Unless you want to subclass a view that is being animated and override the View.onAnimationEnd() you are pretty much out of options. Under normal circumstances however you don&#8217;t want to create another subclass.

There is one way to get this information somewhat reliably. It is to register a callback with Animation.setAnimationListener(&#8230;) . When at first it might seem as the fool-proof way there is a situation where it can cause headache.

Say you want to fade out some views (with different durations) and hide them after the animation has ended so they wouldn&#8217;t pop up again. And after all of the views have finished their animation close the activity all together.

You could go and write this for every animation for every view:

{% codeblock lang:java %}{% raw %}
View viewN = ..;
Animation animN = ...;
animN.setAnimationListener(new AnimationListener() {
  // ...
  void onAnimationEnd(Animation anim)
  {
    viewN.setVisibility(View.GONE);
    // Check if all the views have been hidden already:
    for (View v : allViews)
      if (v.getVisibility() !== View.GONE)
        return;
    // Close the activity:
    finish();
  }
  // ...
});
viewN.startAnimation(animN);
{% endraw %}{% endcodeblock %}

As you can see it will get out of hand pretty fast if using this approach. You end up repeating yourself over and over, instead you&#8217;d just want to use something like this:

{% codeblock lang:java %}{% raw %}
AnimationListener animListener = new AnimationListener() {
  // ...
  void onAnimationEnd(Animation anim)
  {
    View view = getAnimationView(anim);
    view.setVisibility(View.GONE);
    // Check if all the views have been hidden already:
    for (View v : allViews)
      if (v.getVisibility() !== View.GONE)
        return;
    // Close the activity:
    finish();
  }
  // ...
};
// ...
anim1.setAnimationListener(animListener);
anim2.setAnimationListener(animListener);
// ...
{% endraw %}{% endcodeblock %}

And use it with all the animations. This is possible with an hashtable mapping animations to views, so that magic function getAnimactionView(Animation a) will just have to look it up from the hashtable and return it. The catch is that you can&#8217;t use the same animation for many views.