---
title: Getting started with Android C2DM
author: Mart
excerpt: "Having realized you need push notifications in your Andorid application you head over to the official C2DM page to read more about it. After finishing it you still don't have a clear understanding on how to actually implement the 3rd party application server so that it would satisfy all of the criteria mentioned there. Here we will go through the very first steps to get you started: signing up for C2DM and getting a better understanding on how to authenticate with Google so you could access the API."
layout: post
permalink: /getting-started-with-android-c2dm/
categories:
  - Guides
tags:
  - Android
  - C2DM
  - Google APIs
  - OAuth 2.0
---
**UPDATE:** As of June 26, 2012 C2DM is deprecated and the new official way to send messages to your Android device is the <a title="Google Cloud Messaging" href="http://developer.android.com/guide/google/gcm/index.html" target="_blank">GCM</a> (Google Cloud Messaging). With GCM both ClientLogin and OAuth2 don&#8217;t work any more, so this article is effectively useless.

Having realized you need push notifications in your Andorid application you head over to the <a title="Android Cloud to Device Messaging Framework" href="https://developers.google.com/android/c2dm/" target="_blank">official C2DM page</a> to read more about it. After finishing it you still don&#8217;t have a clear understanding on how to actually implement the 3rd party application server so that it would satisfy all of the criteria mentioned there. Here we will go through the very first steps to get you started: signing up for C2DM and getting a better understanding on how to authenticate with Google so you could access the API.

### Signing Up for C2DM

As Android C2DM is still in early test phases you need to explicitly sign up for it by filling out a form at the <a title="Sign Up For Android Cloud to Device Messaging" href="https://developers.google.com/android/c2dm/signup" target="_blank">sign up page</a>. All of the fields are very straight forward except one, the &#8220;Role (sender) account email&#8221; - what is this &#8220;sender email&#8221;? On the C2DM page it is described as: *This ID is typically role-based rather than being a personal account &#8211; for example, my-app@gmail.com*.

This basically means that you need to create a new Google account, however, it does not specify if the Gmail inbox is required or can it be a plain Google account, maybe even a Google Apps account? Just to be on the safe-side it is wise to create a Google account with a Gmail inbox. You can do this from the <a title="Gmail" href="http://gmail.com/" target="_blank">Gmail page</a> when logged out of your other accounts (or browsing incognito).

When signing up you will be automatically signed up for Google+, but don&#8217;t worry about it as the G+ profile can be easily deleted once you&#8217;ve created the account. You can do so on the <a title="Account overview" href="https://www.google.com/settings/" target="_blank">Account overview</a> page by clicking the &#8220;Delete profile and remove associated Google+ features&#8221; link at the very bottom of it.

Now that your Android app has its very own my.app@gmail.com Google account you can return to the C2DM sign up page and finish completing the form there. After you&#8217;ve submitted the form you will shortly receive an email to the contact email you specified and you will have access to the C2DM APIs.

### Authenticating Yourself for API Access

The next step is getting yourself authenticated for actually communicating with the C2DM API. The C2DM documentation page mentions that you should use ClientLogin for Installed Applications, but the link itself is (as of writing this) dead. Quick search yields the correct location for <a title="ClientLogin for Installed Applications" href="https://developers.google.com/accounts/docs/AuthForInstalledApps" target="_blank">ClientLogin</a> information. And at the top of the page, there is a big notice in red, that this has been deprecated and future code should use OAuth 2.0. Sadly, there is not much information out there about using C2DM with OAuth 2.0.

After reading the documentation about <a title="Using OAuth 2.0 to Access Google APIs" href="https://developers.google.com/accounts/docs/OAuth2" target="_blank">using OAuth 2.0 with Google APIs</a> we realize that we&#8217;ve got 3 different ways to approach this:

1.  Use the deprecated ClientLogin. Out of the three, this is the only one that actually has sample code. But it comes with some caveats &#8211; you never know when Google removes ClientLogin forcing you to update your code and ClientLogin cannot be completely automated, as occasionally it requires captcha completion to successfully get the authentication token.
2.  Use OAuth 2.0 and the <a title="Using OAuth 2.0 for Web Server Applications" href="https://developers.google.com/accounts/docs/OAuth2WebServer" target="_blank">Web Server</a> authentication mechanism to obtain the access token. Even though it doesn&#8217;t seem to be a very good option at first as it requires the user to authorize the application in a web browser, it was to work <a title="OAuth2 with Google C2DM (push)" href="http://aleksmaus.blogspot.co.uk/2012/01/oauth2-with-google-c2dm-push.html" target="_blank">by Alex</a>.
3.  Use OAuth 2.0 and the <a title="Using OAuth 2.0 for Server to Server Applications" href="https://developers.google.com/accounts/docs/OAuth2ServiceAccount" target="_blank">Service Account</a> authentication mechanism to obtain the access token. From all of the OAuth options this actually seems the best one to use for an unattended server application.

The ClientLogin&#8217;s quirk to force captcha checkis is a big no-no for most unattended server applications, so option 1 is out. Even though option 3 seems to be the best suited, it seems that the C2DM does not yet support that OAuth machanism. That leaves us with option 2, using the Web Server authentication with OAuth 2.0.

To get started with OAuth API access you first need to head on over to the <a title="Google APIs Console" href="https://code.google.com/apis/console/" target="_blank">Google APIs Console</a> (while logged in with your application Google account you created previously). Because the C2DM is still in trial phases it does not show up under the Services tab there, but worry not, it still works.

Now you need to go to the API Access tab and create your web application the credentials it can use for OAuth 2.0 protocols. Hit the &#8220;Create another client ID&#8230;&#8221; button and a dialog should pop up. In there you need to select the &#8220;Web application&#8221; under Application type and hit (more options) link to expose 2 text areas. In the &#8220;Authorized Redirect URIs&#8221; text area replace the content with &#8220;[https://code.google.com/oauthplayground/](https://code.google.com/oauthplayground/)&#8221; (you need this in the next steps), and clear the &#8220;Authorized JavaScript Origins&#8221; text area. Hitting the &#8220;Create client ID&#8221; button at the bottom of the dialog window will create a new client ID for you to use.


{% img center /images/content/2012/05/create_web_app_api_access.png 560 576 'Google APIs Console - Create Client ID dialog' 'Create Client ID dialog' %}

The newly created Client ID will appear with it&#8217;s associated email address and client secret. Now we will use Google&#8217;s <a title="OAuth 2.0 Playground" href="https://code.google.com/oauthplayground/" target="_blank">OAuth 2.0 Playground</a> to do the initial authorization and obtain the refresh token, that can be used in your web application to obtain the access token that gives access to the C2DM APIs.

On the OAuth 2.0 Playground page open up the settings menu and fill it in like in the image below, into the OAuth Client ID and OAuth Client secret fields you need to copy the strings from the API Access tab in the API console that appeared after you created the new Client ID.

{% img center /images/content/2012/05/oauth_playground_settings.png 462 582 'OAuth 2.0 Playground - C2DM Settings' 'Playground C2DM Settings' %}

After filling in the configuration in the settings pane, you need to enter &#8220;[https://android.apis.google.com/c2dm](https://android.apis.google.com/c2dm)&#8221; into the &#8220;Input your own scopes&#8221; text box on the left hand side and press Authorize APIs. You will be redirected to another page that asks your permission to use your account &#8211; double check that this is the account you created, not your personal and click &#8220;Allow access&#8221; button.

{% img center /images/content/2012/05/oauth_playground_step1.png 489 134 'OAuth 2.0 Playground - C2DM Scope' 'C2DM Scope' %}

You will be redirected back to the Playgroung page with &#8220;Step 2&#8243; being expanded on the left hand column. There you need to click the &#8220;Exchange authorization code for tokens&#8221; button to acquire the refresh token (if this does not appear double check that your &#8220;Access type&#8221; is Offline in the settings) and access token. Now you need to write down the refresh token as this is the one you will be storing in your web application configuration files for accessing the C2DM APIs.

To quickly test it all worked and you have access to C2DM API, you can use the access token that was returned and curl on the command line like this (don&#8217;t forget to replace the <access\_token> with your access\_token from the previous step):

{% codeblock lang:bash %}{% raw %}
$ curl -k -H 'Authorization: Bearer "access_token"' \
  https://android.apis.google.com/c2dm/send \
  -d 'registration_id=0bcfed8655ad4a0abddb051ac65da432' \
  -d 'collapse_key=0' -d 'data.test=test'
{% endraw %}{% endcodeblock %}

The C2DM should return Error=InvalidRegistration, unless you replaced the registration_id with a value that is actually associated with your sender account.

Now you are ready to write the rest of the server that confirms to the criteria cited in the C2DM documentation. Good luck!