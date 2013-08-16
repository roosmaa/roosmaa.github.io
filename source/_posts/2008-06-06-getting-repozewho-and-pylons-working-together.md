---
title: Getting `repoze.who` and Pylons working together
author: Mart
excerpt: "Finding an authentication middleware for Pylons was quite a challange, because the most advertised middleware (AuthKit) didn't really work. At least I didn't manage to get it to do what I wanted. On Pylons IRC channel someone suggested to take a look at `repoze.who`."
layout: post
permalink: /getting-repozewho-and-pylons-working-together/
syntaxhighlighter_encoded:
  - 1
categories:
  - Guides
tags:
  - Pylons
  - Python
  - repoze.who
---
Finding an authentication middleware for Pylons was quite a challange, because the most advertised middleware (AuthKit) didn&#8217;t really work. At least I didn&#8217;t manage to get it to do what I wanted. On Pylons IRC channel someone suggested to take a look at \`repoze.who\`.  

For me \`repoze.who\` was perfect, as it is lightweight and very flexible. All I had to do was add a couple of lines into the development.ini file and write an authenticator plug-in to handle verifying users against my users table in the DB. And no, writing that plug-in wasn&#8217;t hard, it&#8217;s only a single file with 10 lines of code. I also use the RedirectingFormPlugin, to have complete control over the login procedure.

The RedurectingFormPlugin adds a GET paremeter to the url indicating where the request came from. But unfortunatelly the ErrorDocuments middleware changed some parameters in the request object and that &#8216;came from&#8217; parameter got to be an url pointing to the error document.

The solution was to add the \`repoze.who\` middleware before the ErrorDocuments, as using the filter-with parameter adds it to the end of the processing pipeline. (I don&#8217;t have any proof of that, though. Just an educated guess.) The \`repoze.who\` README had an example of how to do it, but as I prefer having who.ini somewhere to 15+ lines of code, I exploited the config parser to achieve it in just 2.5 extra lines. That&#8217;s really what this post is about, so here goes:

1.  In your development.ini, add the following lines to the [app:main] section:
{% codeblock lang:ini %}{% raw %}
who.config_file = %(here)s/who.ini
who.log_level = debug
who.log_file = stdout
{% endraw %}{% endcodeblock %}

2.  In your config/middleware: 
    *   Add the following line to the top of the file:
        `from repoze.who.config import make_middleware_with_config as make_who_with_config`
    *   Add the following line under the comment that states that there should be custom middleware:
        `app = make_who_with_config(app, global_conf, app_conf['who.config_file'], app_conf['who.log_file'], app_conf['who.log_level'])`

That&#8217;s it. Enjoy.