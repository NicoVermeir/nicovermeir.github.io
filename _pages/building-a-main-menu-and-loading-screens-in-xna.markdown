---
layout: post
title: "Building a main menu and loading screens in XNA"
date: 2011-07-13 20:10:01 +0200
comments: false
author: Nico Vermeir
sitemap: false
permalink: /building-a-main-menu-and-loading-screens-in-xna.html
redirect_from:
 - /blog/page/Building-a-main-menu-and-loading-screens-in-XNA.aspx.html
 - /blog/page/building-a-main-menu-and-loading-screens-in-xna.aspx.html
---
<div>There are probably many other ways to build menus and loading screens in XNA game development but this is how I currently do it.</div>
<div>For this guide you need:</div>
<ul>
<li>XNA 4.0</li>
<li>Visual Studio 2010 or express edition</li>
<li>C# and some XNA knowledge</li>
</ul>
<div>&nbsp;</div>
<div>My full project is linked at the end of this guide, this code is for demo purposes, feel free to use or adjust it as you see fit. If this guide helps you you can always help my reputation by mentioning me on your blog, Twitter or other social media.</div>
<div>All right, let's get things started! This guide builds a Windows game but also works for Windows Phone 7 and Xbox360 games, just replace the mouse code with controller or touch code.</div>
<div>In Visual Studio start a new XNA game by selecting File &gt; New &gt; Project. In the XNA templates select "Windows Game (4.0)" and name it XNAMenuLoadingDemo.</div>
<div><a href="http://i55.tinypic.com/29dw87b.jpg" target="_blank"><img style="display: block; float: none; margin-left: auto; margin-right: auto;" src="http://i55.tinypic.com/29dw87b.jpg" alt="" width="289" height="200" /></a></div>
<div><br />Once the project has loaded you have created a Windows game, if we run this now all we see is a blue screen (this is a good thing in this case). As you can see the mouse pointer doesn't work in the game, the menu we'll create in this guide uses the mouse so we will have to activate it first. This is done in the Initialize method by adding this line of code:</div>
<div>&nbsp;</div>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="rem">//enable the mousepointer</span></pre>
<pre><span class="lnum"> 2: </span>IsMouseVisible = <span class="kwrd">true</span>;</pre>
</div>
<p>Next step is adding some global variables that will contain textures. <br />Global variables are declared right after the opening bracket of the class, before the constructor. There should allready be 2 global variables in the game called &ldquo;graphics&rdquo; and &ldquo;spriteBatch&rdquo;, place the following code after these two:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">private Texture2D</span>  orb;</pre>
<pre><span class="lnum"> 2: </span><span class="kwrd">private Texture2D</span> startButton;</pre>
<pre class="alt"><span class="lnum"> 3: </span><span class="kwrd">private Texture2D</span> exitButton;</pre>
<pre><span class="lnum"> 4: </span><span class="kwrd">private Texture2D</span> pauseButton;</pre>
<pre class="alt"><span class="lnum"> 5: </span><span class="kwrd">private Texture2D</span> resumeButton;</pre>
<pre><span class="lnum"> 6: </span><span class="kwrd">private Texture2D</span> loadingScreen;</pre>
</div>
<p><span style="font-family: Arial;">As you may notice we declared five textures for buttons, that&rsquo;s because in XNA there&rsquo;s no such thing as a button. <br />So as a workaround we create images that look like buttons and make them <br />do something.</span></p>
<p>All right, next step is declaring some vectors. Vectors are often used in XNA for positioning elements because a Vector2 has two properties called X and Y. Add the following lines of code under the textures:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">private Vector2</span> orbPosition;</pre>
<pre><span class="lnum"> 2: </span><span class="kwrd">private Vector2</span> startButtonPosition;</pre>
<pre class="alt"><span class="lnum"> 3: </span><span class="kwrd">private Vector2</span> exitButtonPosition;</pre>
<pre><span class="lnum"> 4: </span><span class="kwrd">private Vector2</span> resumeButtonPosition;</pre>
</div>
<p>I deliberately only use vectors to position three of the five buttons because I want to show both ways of positioning an element in an XNA game.</p>
<p>Next up is some floats for the game:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">private</span> <span class="kwrd">const</span> <span class="kwrd">float</span> OrbWidth = 50f;</pre>
<pre><span class="lnum"> 2: </span><span class="kwrd">private</span> <span class="kwrd">const</span> <span class="kwrd">float</span> OrbHeight = 50f;</pre>
<pre class="alt"><span class="lnum"> 3: </span><span class="kwrd">private</span> <span class="kwrd">float</span> speed = 1.5f;</pre>
</div>
<p>The game that we&rsquo;re building won&rsquo;t be anything playable, it&rsquo;s just a red orb bouncing back and forth horizontally. But it will have a start menu, a loading screen and a pause button that are fully functional. The floats that we declared are contain the dimensions of the orb and the speed by which the orb moves back and forth.</p>
<p>We&rsquo;re almost there, just a few more variables and we can start making some magic happen. Add these variables:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">private Thread</span> backgroundThread;</pre>
<pre><span class="lnum"> 2: </span><span class="kwrd">private</span> <span class="kwrd">bool</span> isLoading = <span class="kwrd">false</span>;</pre>
<pre class="alt"><span class="lnum"> 3: </span><span class="kwrd">MouseState</span>  mouseState;</pre>
<pre><span class="lnum"> 4: </span><span class="kwrd">MouseState</span>  previousMouseState;</pre>
<pre>&nbsp;</pre>
</div>
<p>I will explain about these variables when we use them.</p>
<p>Now before we continue, let me explain something about XNA that you maybe already know. <br />XNA has 2 specific methods called Update() and Draw(). These two methods are called 60 times per second in Windows and Xbox360 games and 30 times per second in Windows Phone 7 games. All the game&rsquo;s logic should go in the Update() method while everything concerning what is showing on the screen should go in the Draw() method. So a game doesn&rsquo;t really have an implementation for menus and pausing, it&rsquo;s up to the developer to create something that will take care of that.</p>
<p>The method I&rsquo;m describing here uses game states, this is something that you can find in the game examples on <a href="http://create.msdn.com">http://create.msdn.com</a>&nbsp;&nbsp;</p>
<p>Game state isn&rsquo;t something that&rsquo;s build in to XNA, we have to build it ourselves. Best way to do this is by using an Enumerator. This code goes in the same place as the global variables we declared a minute ago.</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">enum</span> GameState</pre>
<pre><span class="lnum"> 2: </span>        {</pre>
<pre class="alt"><span class="lnum"> 3: </span>            StartMenu,</pre>
<pre><span class="lnum"> 4: </span>            Loading,</pre>
<pre class="alt"><span class="lnum"> 5: </span>            Playing,</pre>
<pre><span class="lnum"> 6: </span>            Paused</pre>
<pre class="alt"><span class="lnum"> 7: </span>        }</pre>
</div>
<div class="csharpcode">&nbsp;</div>
<p><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-2.aspx" target="_top">Let's go on to the next page where we'll finally see something happen -&gt;</a></p>
{% include imported_disclaimer.html %}
