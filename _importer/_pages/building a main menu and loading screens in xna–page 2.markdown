---
layout: post
title: "building a main menu and loading screens in xna–page 2"
date: 2011-07-14 14:24:24 +0200
comments: false
author: Nico Vermeir
sitemap: false
permalink: /building a main menu and loading screens in xna–page 2.html
redirect_from:
 - /blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-2.aspx.html
 - /blog/page/building-a-main-menu-and-loading-screens-in-xna-page-2.aspx.html
---
<p>All right, now we have our variables in place. In case you&rsquo;ve missed it, you can find page 1 <a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA.aspx">here</a>.</p>
<p>We&rsquo;ll start by building the bouncing orb, then adding in the start menu, the loading screen and the pause button.</p>
<p>Normally you would load all textures and content in the LoadContent() method, this method is called once when the game launches. This is all good when the game is very small and has no menu. We will build a LoadGame() method that does all the loading of game textures and at the end of this guide the method will be called when the player clicks start.</p>
<p>As said, the menu will be introduced later, for now create the LoadGame() method right under the Draw() method.</p>
<div class="csharpcode">
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">void</span> LoadGame()</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    <span class="rem">//load the game images into the content pipeline</span></pre>
<pre><span class="lnum"> 4: </span>    orb = Content.Load&lt;Texture2D&gt;(<span class="str">@"orb"</span>);</pre>
<pre class="alt"><span class="lnum"> 5: </span>    </pre>
<pre><span class="lnum"> 6: </span>    <span class="rem">//set the position of the orb in the middle of the gamewindow</span></pre>
<pre class="alt"><span class="lnum"> 7: </span>    orbPosition = <span class="kwrd">new</span> Vector2((GraphicsDevice.Viewport.Width / 2) - (OrbWidth / 2),</pre>
<pre><span class="lnum"> 8: </span>                    (GraphicsDevice.Viewport.Height / 2) - (OrbHeight / 2));</pre>
<pre class="alt"><span class="lnum"> 9: </span>}</pre>
</div>
</div>
<p>Line number 4 loads an image into the content pipeline as a 2D texture. To do this, we need to add a texture to our project. Save this picture to your pc and remember it&rsquo;s location.</p>
<p><img style="display: block; float: none; margin-left: auto; margin-right: auto;" src="http://i53.tinypic.com/29nfomc.jpg" alt="" /></p>
<p>This is the orb that&rsquo;ll be moving back and forth. To use it, right-click the content project in your solution explorer, select Add &gt; existing file and select the image.</p>
<p>Line 7 and 8 set the position of the orb, initially this will be the middle of the gamescreen. This is done by taking the width and height from the screen and dividing it by two. Since this will place the upper left corner of the image in the middle we need to substract half the image width and height from the respective width and height.</p>
<p>Now we have a method that loads something in our content pipeline, now we need to call this method. For now we&rsquo;ll just use the Initialize() method for this. Change it so it looks like this:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">protected</span> <span class="kwrd">override</span> <span class="kwrd">void</span> Initialize()</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>     <span class="rem">//enable the mousepointer</span></pre>
<pre><span class="lnum"> 4: </span>     IsMouseVisible = <span class="kwrd">true</span>;</pre>
<pre class="alt"><span class="lnum"> 5: </span>&nbsp;</pre>
<pre><span class="lnum"> 6: </span>     LoadGame();</pre>
<pre class="alt"><span class="lnum"> 7: </span>}</pre>
</div>
<p>This will call the LoadGame() method as soon as the game starts. So now we have a method that loads a texture, we call the method when the game launches, all that&rsquo;s left is to draw the texture 60 times per second. To get this done, change the Draw() method so it looks like this:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">protected</span> <span class="kwrd">override</span> <span class="kwrd">void</span> Draw(<span class="kwrd">GameTime</span> gameTime)</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    GraphicsDevice.Clear(<span class="kwrd">Color</span>.CornflowerBlue);</pre>
<pre><span class="lnum"> 4: </span>    spriteBatch.Begin();</pre>
<pre class="alt"><span class="lnum"> 5: </span>&nbsp;</pre>
<pre><span class="lnum"> 6: </span>    <span class="rem">//draw the orb</span></pre>
<pre class="alt"><span class="lnum"> 7: </span>    spriteBatch.Draw(orb, orbPosition, <span class="kwrd">Color</span>.White);</pre>
<pre><span class="lnum"> 8: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 9: </span>    spriteBatch.End();</pre>
<pre><span class="lnum"> 10: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 11: </span>    <span class="kwrd">base</span>.Draw(gameTime);</pre>
<pre><span class="lnum"> 12: </span>}</pre>
</div>
<p>SpriteBatch is an XNA specific class that will take care of rendering everything on the screen. It has a Begin() and an End() method that need to be called, everything between those two methods will be rendered onscreen. It takes three parameters, a texture, a position vector and a color. Color.White says the spritebatch to render the texture in it&rsquo;s original color.</p>
<p>If we run the game now we see a red orb standing in the middle of the screen.</p>
<p><a href="http://i51.tinypic.com/8y7228.jpg" target="_blank"><img style="display: block; float: none; margin-left: auto; margin-right: auto;" src="http://i51.tinypic.com/8y7228.jpg" alt="" width="395" height="249" /></a></p>
<p>Now let&rsquo;s get that little red lazy bum moving! Moving thing around in a game is done in the Update() method. Change it so it looks like this:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">protected</span> <span class="kwrd">override</span> <span class="kwrd">void</span> Update(GameTime gameTime)</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    <span class="rem">// Allows the game to exit</span></pre>
<pre><span class="lnum"> 4: </span>    <span class="kwrd">if</span> (GamePad.GetState(PlayerIndex.One).Buttons.Back == ButtonState.Pressed)</pre>
<pre class="alt"><span class="lnum"> 5: </span>        <span class="kwrd">this</span>.Exit();</pre>
<pre><span class="lnum"> 6: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 7: </span>    <span class="rem">//move the orb</span></pre>
<pre><span class="lnum"> 8: </span>    orbPosition.X += speed;</pre>
<pre class="alt"><span class="lnum"> 9: </span>&nbsp;</pre>
<pre><span class="lnum"> 10: </span>    <span class="rem">//prevent out of bounds</span></pre>
<pre class="alt"><span class="lnum"> 11: </span>    <span class="kwrd">if</span> (orbPosition.X &gt; (GraphicsDevice.Viewport.Width - OrbWidth) || orbPosition.X &lt; 0)</pre>
<pre><span class="lnum"> 12: </span>    {</pre>
<pre class="alt"><span class="lnum"> 13: </span>        speed *= -1;</pre>
<pre><span class="lnum"> 14: </span>    } </pre>
<pre class="alt"><span class="lnum"> 15: </span>    </pre>
<pre><span class="lnum"> 16: </span>    <span class="kwrd">base</span>.Update(gameTime);</pre>
<pre class="alt"><span class="lnum"> 17: </span>}</pre>
</div>
<p>Line 8 increments the X value of the orb&rsquo;s position with the value of the speed variable 60 times every second, at that speed it shows a very fluent animation. The if construction on line 11 checks if the orb goes out of sight and prevents it by inverting the speed value so it makes the orb go the other way.</p>
<p>So our gameplay is finished now, on the next page we will finally introduce the start menu.</p>
<p><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-3.aspx" target="_top">Hurry on to page 3!</a></p>
{% include imported_disclaimer.html %}
