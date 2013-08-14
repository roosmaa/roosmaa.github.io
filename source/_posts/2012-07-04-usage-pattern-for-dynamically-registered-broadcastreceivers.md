---
title: Usage pattern for dynamically registered BroadcastReceivers
author: Mart
excerpt: Dealing with dynamically registered BroadcastReceivers is tricky. This pattern makes it a bit easier.
layout: post
permalink: /usage-pattern-for-dynamically-registered-broadcastreceivers/
categories:
  - Guides
tags:
  - Android
  - Java
---
When registering BroadcastReceivers dynamically in your application, you need to keep track of them and unregister them. And when unregistering you need to make sure that you haven&#8217;t done it before, else you&#8217;ll get an IllegalArgumentException. Another annoyance is when you need to check if you actually registered the receiver already or not.

The following code sample illustrates a nice way to keep track of registered receivers and only allow deleting them once.

<div id="gist3045908" class="gist">
  <div class="gist-file">
    <div class="gist-data gist-syntax">
      <div class="file-data">
        <table cellpadding="0" cellspacing="0" class="lines highlight">
          <tr>
            <td class="line-numbers">
              <span class="line-number" id="file-sample-java-L1" rel="file-sample-java-L1">1</span> <span class="line-number" id="file-sample-java-L2" rel="file-sample-java-L2">2</span> <span class="line-number" id="file-sample-java-L3" rel="file-sample-java-L3">3</span> <span class="line-number" id="file-sample-java-L4" rel="file-sample-java-L4">4</span> <span class="line-number" id="file-sample-java-L5" rel="file-sample-java-L5">5</span> <span class="line-number" id="file-sample-java-L6" rel="file-sample-java-L6">6</span> <span class="line-number" id="file-sample-java-L7" rel="file-sample-java-L7">7</span> <span class="line-number" id="file-sample-java-L8" rel="file-sample-java-L8">8</span> <span class="line-number" id="file-sample-java-L9" rel="file-sample-java-L9">9</span> <span class="line-number" id="file-sample-java-L10" rel="file-sample-java-L10">10</span> <span class="line-number" id="file-sample-java-L11" rel="file-sample-java-L11">11</span> <span class="line-number" id="file-sample-java-L12" rel="file-sample-java-L12">12</span> <span class="line-number" id="file-sample-java-L13" rel="file-sample-java-L13">13</span> <span class="line-number" id="file-sample-java-L14" rel="file-sample-java-L14">14</span> <span class="line-number" id="file-sample-java-L15" rel="file-sample-java-L15">15</span> <span class="line-number" id="file-sample-java-L16" rel="file-sample-java-L16">16</span> <span class="line-number" id="file-sample-java-L17" rel="file-sample-java-L17">17</span> <span class="line-number" id="file-sample-java-L18" rel="file-sample-java-L18">18</span> <span class="line-number" id="file-sample-java-L19" rel="file-sample-java-L19">19</span> <span class="line-number" id="file-sample-java-L20" rel="file-sample-java-L20">20</span> <span class="line-number" id="file-sample-java-L21" rel="file-sample-java-L21">21</span> <span class="line-number" id="file-sample-java-L22" rel="file-sample-java-L22">22</span> <span class="line-number" id="file-sample-java-L23" rel="file-sample-java-L23">23</span> <span class="line-number" id="file-sample-java-L24" rel="file-sample-java-L24">24</span> <span class="line-number" id="file-sample-java-L25" rel="file-sample-java-L25">25</span> <span class="line-number" id="file-sample-java-L26" rel="file-sample-java-L26">26</span> <span class="line-number" id="file-sample-java-L27" rel="file-sample-java-L27">27</span> <span class="line-number" id="file-sample-java-L28" rel="file-sample-java-L28">28</span> <span class="line-number" id="file-sample-java-L29" rel="file-sample-java-L29">29</span> <span class="line-number" id="file-sample-java-L30" rel="file-sample-java-L30">30</span> <span class="line-number" id="file-sample-java-L31" rel="file-sample-java-L31">31</span> <span class="line-number" id="file-sample-java-L32" rel="file-sample-java-L32">32</span> <span class="line-number" id="file-sample-java-L33" rel="file-sample-java-L33">33</span> <span class="line-number" id="file-sample-java-L34" rel="file-sample-java-L34">34</span> <span class="line-number" id="file-sample-java-L35" rel="file-sample-java-L35">35</span> <span class="line-number" id="file-sample-java-L36" rel="file-sample-java-L36">36</span> <span class="line-number" id="file-sample-java-L37" rel="file-sample-java-L37">37</span> <span class="line-number" id="file-sample-java-L38" rel="file-sample-java-L38">38</span> <span class="line-number" id="file-sample-java-L39" rel="file-sample-java-L39">39</span> <span class="line-number" id="file-sample-java-L40" rel="file-sample-java-L40">40</span> <span class="line-number" id="file-sample-java-L41" rel="file-sample-java-L41">41</span> <span class="line-number" id="file-sample-java-L42" rel="file-sample-java-L42">42</span> <span class="line-number" id="file-sample-java-L43" rel="file-sample-java-L43">43</span> <span class="line-number" id="file-sample-java-L44" rel="file-sample-java-L44">44</span> <span class="line-number" id="file-sample-java-L45" rel="file-sample-java-L45">45</span>
            </td>
            
            <td class="line-data">
              <pre class="line-pre"><div class="line" id="file-sample-java-LC1">
  <span class="kn">package</span> <span class="n">net</span><span class="o">.</span><span class="na">roosmaa</span><span class="o">.</span><span class="na">example</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC2">
  &nbsp;
</div>

<div class="line" id="file-sample-java-LC3">
  <span class="kn">import</span> <span class="nn">android.content.BroadcastReceiver</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC4">
  <span class="kn">import</span> <span class="nn">android.content.Context</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC5">
  <span class="kn">import</span> <span class="nn">android.content.Intent</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC6">
  <span class="kn">import</span> <span class="nn">android.content.IntentFilter</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC7">
  &nbsp;
</div>

<div class="line" id="file-sample-java-LC8">
  <span class="kd">public</span> <span class="kd">class</span> <span class="nc">Sample</span>
</div>

<div class="line" id="file-sample-java-LC9">
  <span class="o">{</span>
</div>

<div class="line" id="file-sample-java-LC10">
  <span class="kd">private</span> <span class="n">Context</span> <span class="n">mContext</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC11">
  <span class="kd">private</span> <span class="n">BroadcastReceiver</span> <span class="n">mMyReceiver</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC12">
  
</div>

<div class="line" id="file-sample-java-LC13">
  <span class="c1">// ..</span>
</div>

<div class="line" id="file-sample-java-LC14">
  
</div>

<div class="line" id="file-sample-java-LC15">
  <span class="cm">/** Registeres the {@link MyReceiver} if it hasn&#39;t been registered yet. */</span>
</div>

<div class="line" id="file-sample-java-LC16">
  <span class="kd">private</span> <span class="kt">void</span> <span class="nf">registerReceiver</span><span class="o">()</span>
</div>

<div class="line" id="file-sample-java-LC17">
  <span class="o">{</span>
</div>

<div class="line" id="file-sample-java-LC18">
  <span class="k">if</span> <span class="o">(</span><span class="n">mMyReceiver</span> <span class="o">!=</span> <span class="kc">null</span><span class="o">)</span>
</div>

<div class="line" id="file-sample-java-LC19">
  <span class="k">return</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC20">
  
</div>

<div class="line" id="file-sample-java-LC21">
  <span class="n">mMyReceiver</span> <span class="o">=</span> <span class="k">new</span> <span class="n">MyReceiver</span><span class="o">();</span>
</div>

<div class="line" id="file-sample-java-LC22">
  <span class="n">mContext</span><span class="o">.</span><span class="na">registerReceiver</span><span class="o">(</span><span class="n">mMyReceiver</span><span class="o">,</span> <span class="k">new</span> <span class="n">IntentFilter</span><span class="o">(</span><span class="s">"myAction"</span><span class="o">));</span>
</div>

<div class="line" id="file-sample-java-LC23">
  <span class="o">}</span>
</div>

<div class="line" id="file-sample-java-LC24">
  
</div>

<div class="line" id="file-sample-java-LC25">
  <span class="cm">/** Unregisteres the {@link MyReceiver} if it was registered previously. */</span>
</div>

<div class="line" id="file-sample-java-LC26">
  <span class="kd">private</span> <span class="kt">void</span> <span class="nf">unregisterReceiver</span><span class="o">()</span>
</div>

<div class="line" id="file-sample-java-LC27">
  <span class="o">{</span>
</div>

<div class="line" id="file-sample-java-LC28">
  <span class="k">if</span> <span class="o">(</span><span class="n">mMyReceiver</span> <span class="o">==</span> <span class="kc">null</span><span class="o">)</span>
</div>

<div class="line" id="file-sample-java-LC29">
  <span class="k">return</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC30">
  
</div>

<div class="line" id="file-sample-java-LC31">
  <span class="n">mContext</span><span class="o">.</span><span class="na">unregisterReceiver</span><span class="o">(</span><span class="n">mMyReceiver</span><span class="o">);</span>
</div>

<div class="line" id="file-sample-java-LC32">
  <span class="n">mMyReceiver</span> <span class="o">=</span> <span class="kc">null</span><span class="o">;</span>
</div>

<div class="line" id="file-sample-java-LC33">
  <span class="o">}</span>
</div>

<div class="line" id="file-sample-java-LC34">
  
</div>

<div class="line" id="file-sample-java-LC35">
  <span class="c1">// ..</span>
</div>

<div class="line" id="file-sample-java-LC36">
  
</div>

<div class="line" id="file-sample-java-LC37">
  <span class="kd">private</span> <span class="kd">class</span> <span class="nc">MyReceiver</span> <span class="kd">extends</span> <span class="n">BroadcastReceiver</span>
</div>

<div class="line" id="file-sample-java-LC38">
  <span class="o">{</span>
</div>

<div class="line" id="file-sample-java-LC39">
  <span class="nd">@Override</span>
</div>

<div class="line" id="file-sample-java-LC40">
  <span class="kd">public</span> <span class="kt">void</span> <span class="nf">onReceive</span><span class="o">(</span><span class="n">Context</span> <span class="n">context</span><span class="o">,</span> <span class="n">Intent</span> <span class="n">intent</span><span class="o">)</span>
</div>

<div class="line" id="file-sample-java-LC41">
  <span class="o">{</span>
</div>

<div class="line" id="file-sample-java-LC42">
  <span class="c1">// Do whatever here</span>
</div>

<div class="line" id="file-sample-java-LC43">
  <span class="o">}</span>
</div>

<div class="line" id="file-sample-java-LC44">
  <span class="o">}</span>
</div>

<div class="line" id="file-sample-java-LC45">
  <span class="o">}</span>
</div></pre>
            </td>
          </tr>
        </table>
      </div>
    </div>
    
    <div class="gist-meta">
      <a href="https://gist.github.com/roosmaa/3045908/raw/bfcbc085192c0c851e26ead6c4e14799f4b0e2e5/Sample.java" style="float:right">view raw</a> <a href="https://gist.github.com/roosmaa/3045908#file-sample-java">Sample.java</a> hosted with &#10084; by <a href="https://github.com">GitHub</a>
    </div>
  </div>
</div>