---
title: Entry/exit animations
author: Mart
excerpt: 'When writing FUN2Learn I was presented with a challenge: how to make views animate at the start and end of the activity without creating a big mess in the code.'
layout: post
permalink: /entry-exit-animations/
categories:
  - Guides
tags:
  - Android
  - Animations
  - Java
---
When writing FUN2Learn I was presented with a challenge: how to make views animate at the start and end of the activity without creating a big mess in the code. So I created a helper class to negotiate the animations for registered views and have a central place to trigger them.

In the <a href="http://developer.android.com/reference/android/app/Activity.html#onCreate(android.os.Bundle)" target="_blank">Activity.onCreate()</a> you need to register the views that have entry/exit animations and start the entry animation if the activity is just started:

{% codeblock lang:java %}{% raw %}
View v1, v2, v3;
  AnimationNegotiator negotiator;
  // ...

  @Override
  protected void onCreate(Bundle savedInstanceState)
  {
    animator.register(v1, negotiator);
    animator.register(v2, negotiator);
    animator.register(v3, negotiator);
    // ...
    if (savedInstanceState == null)
      animator.animate(GroupAnimator.ANIMATION_IN);
  }
{% endraw %}{% endcodeblock %}

The GroupAnimator.animate() function can also take a callback as a second argument, which is called when animations end. This is useful for using the GroupAnimator to do exit animations.

To do the exit animation you need to override the [Activity.onKeyDown()][2] method and intercept the <a href="http://developer.android.com/reference/android/view/KeyEvent.html#KEYCODE_BACK" target="_blank">KeyEvent.KEYCODE_BACK</a> key code, starting the exit animations with the callback to actually close the activity when the animations have ended. You do however want to make the implementation so that when pressing the back button twice it would exit immediately.

The AnimationNegotiator is a simple callback that is used to determine the animation used for each of the visible views. When called, it receives the view in question and the animation type (in/out) as arguments and is expected to return the animation to be used. For instance a simple implementation of this could be:

{% codeblock lang:java %}{% raw %}
public class MyActivity extends Activity implements AnimationNegotiator
{
  // ...
  public Animation negotiateAnimation(View v, int animType)
  {
    if (animType == GroupAnimator.ANIMATION_IN)
      return AnimationUtils.loadAnimation(this, R.anim.fade_in);
    if (animType == GroupAnimator.ANIMATION_OUT)
      return AnimationUtils.loadAnimation(this, R.anim.fade_out);

    return null;
  }
  // ...
}
{% endraw %}{% endcodeblock %}

And finally the code for GroupAnimator itself:

{% codeblock lang:java %}{% raw %}
package net.roosmaa.types;

import java.security.InvalidParameterException;
import java.util.ArrayList;
import java.util.Iterator;

import android.view.View;
import android.view.ViewParent;
import android.view.animation.Animation;
import android.view.animation.Animation.AnimationListener;

public class GroupAnimator
{
  public static final int ANIMATION_IN = 0;
  public static final int ANIMATION_OUT = 1;

  public interface AnimationNegotiator
  {
    Animation negotiateAnimation(View v, int animType);
  };

  public interface AnimationProgress
  {
    void animationEnded(View v, int type, int stillRunning);
  }

  private class RegInfo
  {
    public View view;
    public AnimationNegotiator negotiator;
  }

  private final ArrayList&lt;RegInfo&gt; regViews = new ArrayList&lt;RegInfo&gt;();
  private Animation defaultIn;
  private Animation defaultOut;

  public void register(View view, AnimationNegotiator negotiator)
  {
    RegInfo inf = new RegInfo();
    inf.view = view;
    inf.negotiator = negotiator;

    regViews.add(inf);
  }

  public void setDefaultAnimation(int type, Animation animation)
  {
    switch (type)
    {
    case ANIMATION_IN:
      defaultIn = animation;
      break;
    case ANIMATION_OUT:
      defaultOut = animation;
      break;
    default:
      throw new InvalidParameterException("Received an invalid type.");
    }
  }

  public void animate(int type)
  {
    animate(type, null);
  }

  private boolean checkTreeVisible(View v)
  {
    if (v.getVisibility() != View.VISIBLE)
      return false;

    ViewParent vp = v.getParent();
    if (!(vp instanceof View))
      return true;

    View p = (View) vp;
    if (p.getId() == android.R.id.content)
      return true;

    return checkTreeVisible(p);
  }

  public void animate(int type, AnimationProgress monitor)
  {
    AnimationTracker animTrk = null;

    if (monitor != null)
      animTrk = new AnimationTracker(type, monitor);

    for (RegInfo inf : regViews)
    {
      if (!checkTreeVisible(inf.view))
        continue;

      Animation anim = null;
      // Negotiate animation for the current view:
      if (inf.negotiator != null)
        anim = inf.negotiator.negotiateAnimation(inf.view, type);
      // Use default if no animation set:
      if (anim == null)
        anim = type == ANIMATION_IN ? defaultIn : defaultOut;
      // If still no animation, don't animate:
      if (anim == null)
        continue;

      if (animTrk != null)
        animTrk.track(inf.view, anim);

      inf.view.startAnimation(anim);
    }
  }

  private class AnimationTracker implements AnimationListener
  {
    private class TrackInf
    {
      public View view;
      public Animation anim;
    }

    private final int type;
    private final AnimationProgress monitor;
    private final ArrayList&lt;TrackInf&gt; inactive = new ArrayList&lt;TrackInf&gt;();
    private final ArrayList&lt;TrackInf&gt; active = new ArrayList&lt;TrackInf&gt;();

    public AnimationTracker(int type, AnimationProgress monitor)
    {
      this.type = type;
      this.monitor = monitor;
    }

    public void track(View view, Animation anim)
    {
      TrackInf inf = new TrackInf();
      inf.view = view;
      inf.anim = anim;
      inactive.add(inf);

      anim.setAnimationListener(this);
    }

    public void onAnimationStart(Animation animation)
    {
      Iterator&lt;TrackInf&gt; iter = inactive.iterator();
      TrackInf inf;
      while (iter.hasNext())
      {
        inf = iter.next();
        if (inf.anim != animation)
          continue;

        iter.remove();
        active.add(inf);
      }
    }

    public void onAnimationEnd(Animation animation)
    {
      Iterator&lt;TrackInf&gt; iter = active.iterator();
      TrackInf inf;
      while (iter.hasNext())
      {
        inf = iter.next();
        if (inf.anim != animation)
          continue;

        iter.remove();
        monitor.animationEnded(inf.view, type, active.size());
      }

      if (active.size() &lt; 1)
      {
        // Active animations over, remove pending inactive;
        // (if the animation doesn't start that most likelly means that the widget is hidden)

        iter = inactive.iterator();
        while (iter.hasNext())
        {
          inf = iter.next();
          if (inf.view.getAnimation() == inf.anim)
            inf.view.setAnimation(null);
        }
        inactive.clear();
      }
    }

    public void onAnimationRepeat(Animation animation)
    {
    }
  }
}
{% endraw %}{% endcodeblock %}

 [2]: http://developer.android.com/reference/android/app/Activity.html#onKeyDown(int, android.view.KeyEvent)