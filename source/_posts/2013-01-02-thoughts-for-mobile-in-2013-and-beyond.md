---
title: Thoughts for mobile in 2013 and beyond
author: Mart
layout: post
permalink: /thoughts-for-mobile-in-2013-and-beyond/
categories:
  - News
  - Thoughts
tags:
  - .NET
  - Android
  - iOS
  - Jolla
  - Linux
  - Mobile
  - Windows Phone
---
Today Canonical <a title="Engadget: Canonical announces Ubuntu for smartphones, we go hands-on" href="http://www.engadget.com/2013/01/02/ubuntu-for-smartphones/" target="_blank">announced</a> the <a title="Ubuntu for phones" href="http://www.ubuntu.com/devices/phone" target="_blank">mobile version</a> of Ubuntu. For me as a Linux fan this is very exciting news, however, the developer in me screams: &#8220;Not another phone I need to target when writing mobile apps&#8221;. I&#8217;m surely not the only one thinking that.

Currently the major smartphone OSes on the market are Apple iOS, Google Android (and Microsoft Windows Phone). Upcoming OSes that I know of and follow are: Mozilla&#8217;s <a title="Firefox OS" href="http://www.mozilla.org/en-US/firefoxos/" target="_blank">Firefox OS</a>, <a title="Jolla" href="http://jolla.com/" target="_blank">Jolla&#8217;s</a> <a title="Sailfish OS" href="https://sailfishos.org/" target="_blank">Sailfish</a>, Blackberry&#8217;s <a title="Blackberry 10" href="http://global.blackberry.com/blackberry-10.html" target="_blank">BB10</a>, Samsung&#8217;s <a title="Tizen" href="https://www.tizen.org/" target="_blank">Tizen</a> and Canonical&#8217;s Ubuntu. Some of those will be released this year, other in the following years. Some might not be released at all and new ones will also try to enter the market.

The current generation of devices mostly have a OS specific user experience (with an exception of Android where manufacturers can somewhat customise the experience). The clear trend is that device manufacturers and operators want to differentiate themselves with different user experiences and we (as consumers) also want to have a unique device that separates us from our friends, which fits with our personalities &#8211; meaning there is demand and room for different operating systems.

If a new device is to succeed it has to have quite a large repository of apps that the user can use. That can be a taunting task for the newcomers as getting developers to make an app for their device is next to impossible due to the huge costs associated with it. In most cases that process involves rewriting the application from scratch in a different language.

Here is a list of the official languages and native (UI) frameworks for each of the platforms:

*   Android &#8211; Java w/ Android framework
*   iOS &#8211; objC w/ Cocoa
*   Windows Phone &#8211; C# w/ Silverlight
*   Sailfish &#8211; C++ w/ QML
*   Firefox OS &#8211; JavaScript w/ HTML5
*   BB10 &#8211; C++ w/ QML
*   Tizen &#8211; C w/ <a title="E17" href="http://www.enlightenment.org/" target="_blank">Enlightenment Foundation Libraries</a>
*   Ubuntu &#8211; C++ w/ QML

As anyone who has been involved in the app developing process knows, it&#8217;s a major headache developing two mostly identical apps, but it&#8217;s more or less manageable. Developing the app for even more platforms would be infeasible (especially financially).

We need tools, which would enable us to stop writing the same code over and over again for multiple platforms. There are various technologies available which do just that and can be used to create 3rd party apps, the most popular of which is the over-hyped HTML5. Others include Flash (for iOS, Android), .NET with Xamarin&#8217;s Mono products (for Android, iOS), various JavaScript based abstraction layers (<a title="PhoneGap" href="http://phonegap.com/" target="_blank">PhoneGap</a>, <a title="Titanium" href="http://www.appcelerator.com/" target="_blank">Titanium</a>, etc) and the list goes on.

Many see HTML5 as the silver bullet here, but I tend to disagree for a couple of reasons. First the bar for performance was set really high by Apple when they released iOS and currently HTML5 cannot get anywhere near it. It is also quite difficult to get a single sane way of integrating with the device user experience due to the fragmented nature of HTML and browser technologies.

Flash based apps do run fine on iOS and Android, but they lack integration with the look and feel of the underlying device. Meaning the technology is only useful for games, where the user doesn&#8217;t expect them to integrate well with the system. There is also a problem of being locked into the platform more than with other technologies &#8211; when Adobe decides to discontinue their Flash products (or export to iOS/Android feature) that&#8217;s that.

The <a title="Xamarin" href="http://xamar.in/r/Roosmaa/xamarin.com" target="_blank">Xamarin</a> team has brought the powerful C# language and .NET framework to both <a title="Xamarin" href="http://xamar.in/r/Roosmaa/xamarin.com" target="_blank">iOS and Android platforms</a>. They have created bindings for almost 100% of the platform provided APIs, so the applications written using their technologies are as good as the ones written with the official platform frameworks. This means that when targeting multiple platforms (Andorid, iOS or Windows Phone) most of the code, which doesn&#8217;t deal with the UI directly, can be reused &#8211; resulting in fewer bugs, faster development time and decreased costs while still providing the user with a native performance and look-n-feel they&#8217;ve grown accustomed to. And because it&#8217;s all based on the open-source <a title="Mono" href="http://www.mono-project.com/" target="_blank">Mono project</a> it is all true and tested, it&#8217;s fast and stable &#8211; even <a title="Unity - Game engine" href="http://unity3d.com/" target="_blank">Unity</a>, the powerful gaming engine, uses Mono as it&#8217;s internal game logic engine.

The last technology, which promises extensive code reuse, native performance and device specific UX is Digia&#8217;s <a title="Qt Quick" href="http://doc.qt.digia.com/qt/qtquick.html" target="_blank">Qt Quick</a> (QML). Most of the new upcoming mobile OS&#8217;es use this technology and Digia <a title="Digia to Acquire Qt from Nokia" href="http://qt.digia.com/About-us/News/Digia-to-Acquire-Qt-from-Nokia/" target="_blank">is committed</a> to enabling the use of this technology on iOS, Android and even Windows 8 devices. The power of Qt Quick is that it separates app code and UI very well, enabling designers to quickly create and experiment with UIs for different form factors and devices. The moment it is available on more than one smartphone platform, this technology will become a serious contender when developing apps for more than one platform, which is one of the reasons why most of the upcoming OSes are using it for their native apps.

And now for some predictions for 2013.

Instead of having to write and maintain several versions for the app in objC, Java and .NET companies and developers will be looking for technologies like .NET/Mono and QML to write performance critical apps and JS/HTML5 to write not-so performance critical apps. Reusing code enables more innovation on the UI side for multiple platforms.

This means that when currently objC/iOS and Java/Android developers are very valuable to companies creating mobile apps, their value will decrease towards the end of the year and .NET/iOS+Android+WP and C++/QML developers will become a sought-after employee in 2014.

There will be many more smartphones which aren&#8217;t iOS or Android devices and which will look and feel awesome, they will have access to most Android apps in addition to apps optimised for those devices. The hardware manufacturers, most of whom currently have almost no way to differentiate their offerings, can regain market share and release cool products that the consumers will want to buy. (Except Nokia. Sorry, but you dance to Microsoft&#8217;s slow and steady beat now. Maybe next year. Probably 2015 though.)

This also means that the user experience will range vastly from device to device. Giving room for experimentation and much more choice. Apps will be available everywhere and they will fit in and be of better quality. The overall application store concept will transcend platforms and a competition of who can provide the best analytical and developer tooling will start.

And now, lets wait and see what the next 12 months brings us; whatever it is, it won&#8217;t be boring!