---
layout: post
title: "building a main menu and loading screens in xna–page 4"
date: 2011-07-15 11:12:53 +0200
comments: false
author: Nico Vermeir
sitemap: false
permalink: /building a main menu and loading screens in xna–page 4.html
redirect_from:
 - /blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-4.aspx.html
 - /blog/page/building-a-main-menu-and-loading-screens-in-xna-page-4.aspx.html
---
<ul>
<li><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA.aspx" target="_top">Page 1</a></li>
<li><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA&ndash;Page-2.aspx" target="_top">Page 2</a></li>
<li><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA&ndash;Page-3.aspx" target="_top">Page 3</a></li>
</ul>
<p>All right let&rsquo;s get some loading magic done! Since this is a very small project there won&rsquo;t be a lot of loading going on, to counter this we will have the loading method sleep for 3 seconds. To do this, change the LoadGame() method so it looks like this:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">void</span> LoadGame()</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    <span class="rem">//load the game images into the content pipeline</span></pre>
<pre><span class="lnum"> 4: </span>    orb = Content.Load&lt;Texture2D&gt;(<span class="str">@"orb"</span>);</pre>
<pre class="alt"><span class="lnum"> 5: </span>    </pre>
<pre><span class="lnum"> 6: </span>    <span class="rem">//set the position of the orb in the middle of the gamewindow</span></pre>
<pre class="alt"><span class="lnum"> 7: </span>    orbPosition = <span class="kwrd">new</span> Vector2((GraphicsDevice.Viewport.Width / 2) - (OrbWidth / 2),</pre>
<pre><span class="lnum"> 8: </span>                     (GraphicsDevice.Viewport.Height / 2) - (OrbHeight / 2));</pre>
<pre class="alt"><span class="lnum"> 9: </span>&nbsp;</pre>
<pre><span class="lnum"> 10: </span>    <span class="rem">//since this will go to fast for this demo's purpose, wait for 3 seconds</span></pre>
<pre class="alt"><span class="lnum"> 11: </span>    Thread.Sleep(3000);</pre>
<pre><span class="lnum"> 12: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 13: </span>    <span class="rem">//start playing</span></pre>
<pre><span class="lnum"> 14: </span>    gameState = GameState.Playing;</pre>
<pre class="alt"><span class="lnum"> 15: </span>    isLoading = </pre>
<span style="color: #0000ff;">true</span>
<pre class="alt">;</pre>
<pre><span class="lnum"> 16: }</span></pre>
<pre>&nbsp;</pre>
<pre><span class="lnum">To be able to make Thread.Sleep work we&rsquo;ll need to add the System.Threading namespace, add the</span></pre>
<pre><span class="lnum">following line all the way at the top of your code:</span></pre>
<pre>&nbsp;</pre>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">using</span> System.Threading;</pre>
</div>
</div>
<div class="csharpcode">&nbsp;</div>
<div class="csharpcode"><span style="color: #606060;">Next we&rsquo;ll need some graphics for our loading screen, add this line of code in the LoadContent() method:</span></div>
<div class="csharpcode">&nbsp;</div>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="rem">//load the loading screen</span></pre>
<pre><span class="lnum"> 2: </span>loadingScreen = Content.Load&lt;Texture2D&gt;(<span class="str">@"loading"</span>);</pre>
</div>
<p><span style="color: #606060; font-family: Consolas;">As usual we need to add an image to the Content project for the loading screen.</span></p>
<p><img style="display: block; float: none; margin-left: auto; margin-right: auto;" src="http://i52.tinypic.com/2usft6o.jpg" alt="" /></p>
<p>This can also be done with text instead of an image, or with some animation like a progress bar or a message that says what is loading at that time.</p>
<p>Now the next part can be tricky, we need to show the loading screen 60 times per second while loading content into the pipeline, that&rsquo;s two actions at the same time so we need a second thread. The creation of the second thread will happen in the Update(GameTime gameTime) method when the game state equals loading and the boolean isLoading is false. If we don&rsquo;t use the boolean the game will initiate the second thread 60 times per second and start loading the same content over and over again until it crashes. To get all this done, change the Update(GameTime gameTime) method so it looks like this:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">protected</span> <span class="kwrd">override</span> <span class="kwrd">void</span> Update(GameTime gameTime)</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    <span class="rem">// Allows the game to exit</span></pre>
<pre><span class="lnum"> 4: </span>    <span class="kwrd">if</span> (GamePad.GetState(PlayerIndex.One).Buttons.Back == ButtonState.Pressed)</pre>
<pre class="alt"><span class="lnum"> 5: </span>        <span class="kwrd">this</span>.Exit();</pre>
<pre><span class="lnum"> 6: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 7: </span>    <span class="rem">//load the game when needed</span></pre>
<pre><span class="lnum"> 8: </span>    <span class="rem">//isLoading bool is to prevent the LoadGame method from being called 60 times a seconds</span></pre>
<pre class="alt"><span class="lnum"> 9: </span>    <span class="kwrd">if</span> (gameState == GameState.Loading &amp;&amp; !isLoading) </pre>
<pre><span class="lnum"> 10: </span>    {</pre>
<pre class="alt"><span class="lnum"> 11: </span>        <span class="rem">//set backgroundthread</span></pre>
<pre><span class="lnum"> 12: </span>        backgroundThread = <span class="kwrd">new</span> Thread(LoadGame);</pre>
<pre class="alt"><span class="lnum"> 13: </span>        isLoading = <span class="kwrd">true</span>;</pre>
<pre><span class="lnum"> 14: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 15: </span>        <span class="rem">//start backgroundthread</span></pre>
<pre><span class="lnum"> 16: </span>        backgroundThread.Start();</pre>
<pre class="alt"><span class="lnum"> 17: </span>    }</pre>
<pre><span class="lnum"> 18: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 19: </span>    <span class="rem">//move the orb if the game is in progress</span></pre>
<pre><span class="lnum"> 20: </span>    <span class="kwrd">if</span> (gameState == GameState.Playing)</pre>
<pre class="alt"><span class="lnum"> 21: </span>    {</pre>
<pre><span class="lnum"> 22: </span>        <span class="rem">//move the orb</span></pre>
<pre class="alt"><span class="lnum"> 23: </span>        orbPosition.X += speed;</pre>
<pre><span class="lnum"> 24: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 25: </span>        <span class="rem">//prevent out of bounds</span></pre>
<pre><span class="lnum"> 26: </span>        <span class="kwrd">if</span> (orbPosition.X &gt; (GraphicsDevice.Viewport.Width - OrbWidth) || orbPosition.X &lt; 0)</pre>
<pre class="alt"><span class="lnum"> 27: </span>        {</pre>
<pre><span class="lnum"> 28: </span>            speed *= -1;</pre>
<pre class="alt"><span class="lnum"> 29: </span>        } </pre>
<pre><span class="lnum"> 30: </span>    }</pre>
<pre class="alt"><span class="lnum"> 31: </span>&nbsp;</pre>
<pre><span class="lnum"> 32: </span>    <span class="rem">//wait for mouseclick</span></pre>
<pre class="alt"><span class="lnum"> 33: </span>    mouseState = Mouse.GetState();</pre>
<pre><span class="lnum"> 34: </span>    <span class="kwrd">if</span> (previousMouseState.LeftButton == ButtonState.Pressed &amp;&amp; </pre>
<pre class="alt"><span class="lnum"> 35: </span>        mouseState.LeftButton == ButtonState.Released)</pre>
<pre><span class="lnum"> 36: </span>    {</pre>
<pre class="alt"><span class="lnum"> 37: </span>        MouseClicked(mouseState.X, mouseState.Y);</pre>
<pre><span class="lnum"> 38: </span>    }</pre>
<pre class="alt"><span class="lnum"> 39: </span>&nbsp;</pre>
<pre><span class="lnum"> 40: </span>    previousMouseState = mouseState;</pre>
<pre class="alt"><span class="lnum"> 41: </span>&nbsp;</pre>
<pre><span class="lnum"> 42: </span>    <span class="kwrd">if</span> (gameState == GameState.Playing &amp;&amp; isLoading)</pre>
<pre class="alt"><span class="lnum"> 43: </span>    {</pre>
<pre><span class="lnum"> 44: </span>        LoadGame();</pre>
<pre class="alt"><span class="lnum"> 45: </span>        isLoading = <span class="kwrd">false</span>;</pre>
<pre><span class="lnum"> 46: </span>    }</pre>
<pre class="alt"><span class="lnum"> 47: </span>    <span class="kwrd">base</span>.Update(gameTime);</pre>
<pre><span class="lnum"> 48: </span>}</pre>
<pre>&nbsp;</pre>
</div>
<p>Line 12 declares a second thread, in it&rsquo;s constructor it expects a method that will run in that thread. We then set the isLoading boolean to true so we won&rsquo;t enter the if construction a second time and call the Start() method on the thread. At that time the LoadGame() method kicks in in the background while the main thread continues to call Update and Draw 60 times per second.</p>
<p>Next up is the Draw() method, place this code between spriteBatch.Begin() and spriteBatch.End()</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="rem">//show the loading screen when needed</span></pre>
<pre><span class="lnum"> 2: </span><span class="kwrd">if</span> (gameState == GameState.Loading)</pre>
<pre class="alt"><span class="lnum"> 3: </span>{</pre>
<pre><span class="lnum"> 4: </span>    spriteBatch.Draw(loadingScreen, <span class="kwrd">new</span> Vector2((GraphicsDevice.Viewport.Width / 2) - </pre>
<pre class="alt"><span class="lnum"> 5: </span>                       (loadingScreen.Width / 2), (GraphicsDevice.Viewport.Height / 2) - </pre>
<pre><span class="lnum"> 6: </span>                       (loadingScreen.Height / 2)), Color.YellowGreen);</pre>
<pre class="alt"><span class="lnum"> 7: </span>}</pre>
</div>
<p>Now one final step is needed, the game state needs to enter the loading state, this is done when the player clicked the start button. To do this, in the MouseClick() method change this line of code:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span>gameState = GameState.Playing;</pre>
</div>
<p>With this line of code:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span>gameState = GameState.Loading;</pre>
</div>
<p>If we run the game now and click the Start button the loading screen will show up for about 3 seconds and then the orb will start it&rsquo;s journey.</p>
<p>Okay, we&rsquo;re almost there, all that&rsquo;s left is the pause button which is pretty similar to the start menu.</p>
<p><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-5.aspx" target="_top">Head on to the next page to finish this guide! &ndash;&gt;</a></p>
{% include imported_disclaimer.html %}
