---
title: Three examples of how not to write code
author: Mart
excerpt: I received a project written by another coder who was sacked which contained some pretty good examples on how NOT to write a web page.
layout: post
permalink: /three-examples-of-how-not-to-write-code/
syntaxhighlighter_encoded:
  - 1
categories:
  - Thoughts
tags:
  - PHP
  - Rant
---
I received a project written by another coder who was sacked which contained some pretty good examples on how NOT to write a web page.

**Example #1:**

This is the pseudo-code representation of the PHP code I received:

`query('SELECT * FROM users');<br />
foreach user:<br />
->if (user.username == my_username):<br />
-->if (user.password == my_password):<br />
--># Do some other checks...<br />
--># User is allowed to log in:<br />
-->break;`

Why not use this code? Because it is inefficient! Let the database do the filtering of usernames and then run your checks.  
<span style="background-color: #ffffff;"><br /> </span> **Example #2:**

Edit page of an item. The edit form comes from a PHP script. The previous values for the form fields are filled in how? With AJAX of course! Why bother with default values for the form fields? AJAX is so much cooler!

But seriously, who does crap like that? It&#8217;s just wrong on so many levels. AJAX has it&#8217;s uses, but this certainly isn&#8217;t one of them.

**Example #3:**

We need to create a web page with a sidepanel with 3 tabs. How do we do it? Of course we write the main page first. Then we add the code for the first tab of the side panel.

Now comes the tricky part: We copy paste the page and sidepanel code, modify the sidepanel so that it becomes tab number 2 and add some if statements around those copy pasted sections. And then we repeat it for 3rd tab.

**Conclusion**

Schools don&#8217;t teach students anything. Don&#8217;t go to school to learn basic IT, grab a book and dive into open source code!

Ok, I might be wrong with the last statement, as I really don&#8217;t know what is being taught in universities on a programming related course, but I sure am disappointed in the quality of code the graduates write.