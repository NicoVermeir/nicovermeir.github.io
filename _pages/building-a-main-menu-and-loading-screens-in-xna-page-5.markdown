---
layout: post
title: "Building a main menu and loading screens in XNA–Page 5"
date: 2011-07-15 13:09:38 +0200
comments: false
author: Nico Vermeir
sitemap: false
permalink: /building-a-main-menu-and-loading-screens-in-xna-page-5.html
redirect_from:
 - /blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-5.aspx.html
 - /blog/page/building-a-main-menu-and-loading-screens-in-xna-page-5.aspx.html
---
<ul>
<li><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA.aspx">Page 1</a></li>
<li><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-2.aspx">Page 2</a></li>
<li><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-3.aspx">Page 3</a></li>
<li><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-4.aspx">Page 4</a></li>
</ul>
<p>In this final chapter we will add a pause and resume button to our game. Let&rsquo;s start off by adding the images to the content project.</p>
<p><img style="display: block; float: none; margin-left: auto; margin-right: auto;" src="http://i51.tinypic.com/2d0xvd.jpg" alt="" /></p>
<p>&nbsp;</p>
<p><img style="display: block; float: none; margin-left: auto; margin-right: auto;" src="http://i51.tinypic.com/2w6uw6f.jpg" alt="" /></p>
<p>These images can be loaded into the content pipeline from the LoadGame() method. Adjust it so it looks like this:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">void</span> LoadGame()</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    <span class="rem">//load the game images into the content pipeline</span></pre>
<pre><span class="lnum"> 4: </span>    orb = Content.Load&lt;Texture2D&gt;(<span class="str">@"orb"</span>);</pre>
<pre class="alt"><span class="lnum"> 5: </span>    pauseButton = Content.Load&lt;Texture2D&gt;(<span class="str">@"pause"</span>);</pre>
<pre><span class="lnum"> 6: </span>    resumeButton = Content.Load&lt;Texture2D&gt;(<span class="str">@"resume"</span>);</pre>
<pre class="alt"><span class="lnum"> 7: </span>    resumeButtonPosition = <span class="kwrd">new</span> Vector2((GraphicsDevice.Viewport.Width / 2) - </pre>
<pre><span class="lnum"> 8: </span>                            (resumeButton.Width / 2),</pre>
<pre class="alt"><span class="lnum"> 9: </span>                            (GraphicsDevice.Viewport.Height / 2)-(resumeButton.Height / 2));</pre>
<pre><span class="lnum"> 10: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 11: </span>    <span class="rem">//set the position of the orb in the middle of the gamewindow</span></pre>
<pre><span class="lnum"> 12: </span>    orbPosition = <span class="kwrd">new</span> Vector2((GraphicsDevice.Viewport.Width / 2) - (OrbWidth / 2), </pre>
<pre class="alt"><span class="lnum"> 13: </span>                    (GraphicsDevice.Viewport.Height / 2) - (OrbHeight / 2));</pre>
<pre><span class="lnum"> 14: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 15: </span>    <span class="rem">//since this will go to fast for this demo's purpose, wait for 3 seconds</span></pre>
<pre><span class="lnum"> 16: </span>    Thread.Sleep(3000);</pre>
<pre class="alt"><span class="lnum"> 17: </span>&nbsp;</pre>
<pre><span class="lnum"> 18: </span>    <span class="rem">//start playing</span></pre>
<pre class="alt"><span class="lnum"> 19: </span>    gameState = GameState.Playing;</pre>
<pre><span class="lnum"> 20: </span>    isLoading = <span class="kwrd">false</span>;</pre>
<pre class="alt"><span class="lnum"> 21: </span>}</pre>
</div>
<p>Next is the Draw() method, make it look like this:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">protected</span> <span class="kwrd">override</span> <span class="kwrd">void</span> Draw(GameTime gameTime)</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    GraphicsDevice.Clear(Color.CornflowerBlue);</pre>
<pre><span class="lnum"> 4: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 5: </span>    spriteBatch.Begin();</pre>
<pre><span class="lnum"> 6: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 7: </span>    <span class="rem">//draw the start menu</span></pre>
<pre><span class="lnum"> 8: </span>    <span class="kwrd">if</span> (gameState == GameState.StartMenu)</pre>
<pre class="alt"><span class="lnum"> 9: </span>    {</pre>
<pre><span class="lnum"> 10: </span>        spriteBatch.Draw(startButton, startButtonPosition, Color.White);</pre>
<pre class="alt"><span class="lnum"> 11: </span>        spriteBatch.Draw(exitButton, exitButtonPosition, Color.White);</pre>
<pre><span class="lnum"> 12: </span>    }</pre>
<pre class="alt"><span class="lnum"> 13: </span>&nbsp;</pre>
<pre><span class="lnum"> 14: </span>    <span class="rem">//show the loading screen when needed</span></pre>
<pre class="alt"><span class="lnum"> 15: </span>    <span class="kwrd">if</span> (gameState == GameState.Loading)</pre>
<pre><span class="lnum"> 16: </span>    {</pre>
<pre class="alt"><span class="lnum"> 17: </span>        spriteBatch.Draw(loadingScreen, <span class="kwrd">new</span> Vector2((GraphicsDevice.Viewport.Width / 2) - </pre>
<pre><span class="lnum"> 18: </span>                        (loadingScreen.Width / 2), (GraphicsDevice.Viewport.Height / 2) - </pre>
<pre class="alt"><span class="lnum"> 19: </span>                        (loadingScreen.Height / 2)), Color.YellowGreen);</pre>
<pre><span class="lnum"> 20: </span>    }</pre>
<pre class="alt"><span class="lnum"> 21: </span>&nbsp;</pre>
<pre><span class="lnum"> 22: </span>    <span class="rem">//draw the the game when playing</span></pre>
<pre class="alt"><span class="lnum"> 23: </span>    <span class="kwrd">if</span> (gameState == GameState.Playing)</pre>
<pre><span class="lnum"> 24: </span>    {</pre>
<pre class="alt"><span class="lnum"> 25: </span>        <span class="rem">//orb</span></pre>
<pre><span class="lnum"> 26: </span>        spriteBatch.Draw(orb, orbPosition, Color.White);</pre>
<pre class="alt"><span class="lnum"> 27: </span>&nbsp;</pre>
<pre><span class="lnum"> 28: </span>        <span class="rem">//pause button</span></pre>
<pre class="alt"><span class="lnum"> 29: </span>        spriteBatch.Draw(pauseButton, <span class="kwrd">new</span> Vector2(0, 0), Color.White);</pre>
<pre><span class="lnum"> 30: </span>    }</pre>
<pre class="alt"><span class="lnum"> 31: </span>&nbsp;</pre>
<pre><span class="lnum"> 32: </span>    <span class="rem">//draw the pause screen</span></pre>
<pre class="alt"><span class="lnum"> 33: </span>    <span class="kwrd">if</span> (gameState == GameState.Paused)</pre>
<pre><span class="lnum"> 34: </span>    {</pre>
<pre class="alt"><span class="lnum"> 35: </span>        spriteBatch.Draw(resumeButton, resumeButtonPosition, Color.White);</pre>
<pre><span class="lnum"> 36: </span>    }</pre>
<pre class="alt"><span class="lnum"> 37: </span>&nbsp;</pre>
<pre><span class="lnum"> 38: </span>    spriteBatch.End();</pre>
<pre class="alt"><span class="lnum"> 39: </span>&nbsp;</pre>
<pre><span class="lnum"> 40: </span>    <span class="kwrd">base</span>.Draw(gameTime);</pre>
<pre class="alt"><span class="lnum"> 41: </span>}</pre>
</div>
<p>&nbsp;</p>
<p>The Update() method doesn&rsquo;t need any change because it already checks for mouse click events, so the only thing left is to adjust the MouseClick() method.</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">void</span> MouseClicked(<span class="kwrd">int</span> x, <span class="kwrd">int</span> y)</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    <span class="rem">//creates a rectangle of 10x10 around the place where the mouse was clicked</span></pre>
<pre><span class="lnum"> 4: </span>    Rectangle mouseClickRect = <span class="kwrd">new</span> Rectangle(x, y, 10, 10);</pre>
<pre class="alt"><span class="lnum"> 5: </span>&nbsp;</pre>
<pre><span class="lnum"> 6: </span>    <span class="rem">//check the startmenu</span></pre>
<pre class="alt"><span class="lnum"> 7: </span>    <span class="kwrd">if</span> (gameState == GameState.StartMenu)</pre>
<pre><span class="lnum"> 8: </span>    {</pre>
<pre class="alt"><span class="lnum"> 9: </span>        Rectangle startButtonRect = <span class="kwrd">new</span> Rectangle((<span class="kwrd">int</span>)startButtonPosition.X, </pre>
<pre><span class="lnum"> 10: </span>                                    (<span class="kwrd">int</span>)startButtonPosition.Y, 100, 20);</pre>
<pre class="alt"><span class="lnum"> 11: </span>        Rectangle exitButtonRect = <span class="kwrd">new</span> Rectangle((<span class="kwrd">int</span>)exitButtonPosition.X, </pre>
<pre><span class="lnum"> 12: </span>                                    (<span class="kwrd">int</span>)exitButtonPosition.Y, 100, 20);</pre>
<pre class="alt"><span class="lnum"> 13: </span>&nbsp;</pre>
<pre><span class="lnum"> 14: </span>        <span class="kwrd">if</span> (mouseClickRect.Intersects(startButtonRect)) <span class="rem">//player clicked start button</span></pre>
<pre class="alt"><span class="lnum"> 15: </span>        {</pre>
<pre><span class="lnum"> 16: </span>            gameState = GameState.Loading;</pre>
<pre class="alt"><span class="lnum"> 17: </span>            isLoading = <span class="kwrd">false</span>;</pre>
<pre><span class="lnum"> 18: </span>        }</pre>
<pre class="alt"><span class="lnum"> 19: </span>        <span class="kwrd">else</span> <span class="kwrd">if</span> (mouseClickRect.Intersects(exitButtonRect)) <span class="rem">//player clicked exit button</span></pre>
<pre><span class="lnum"> 20: </span>        {</pre>
<pre class="alt"><span class="lnum"> 21: </span>            Exit();</pre>
<pre><span class="lnum"> 22: </span>        }</pre>
<pre class="alt"><span class="lnum"> 23: </span>    }</pre>
<pre><span class="lnum"> 24: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 25: </span>    <span class="rem">//check the pausebutton</span></pre>
<pre><span class="lnum"> 26: </span>    <span class="kwrd">if</span> (gameState == GameState.Playing)</pre>
<pre class="alt"><span class="lnum"> 27: </span>    {</pre>
<pre><span class="lnum"> 28: </span>        Rectangle pauseButtonRect = <span class="kwrd">new</span> Rectangle(0, 0, 70, 70);</pre>
<pre class="alt"><span class="lnum"> 29: </span>&nbsp;</pre>
<pre><span class="lnum"> 30: </span>        <span class="kwrd">if</span> (mouseClickRect.Intersects(pauseButtonRect))</pre>
<pre class="alt"><span class="lnum"> 31: </span>        {</pre>
<pre><span class="lnum"> 32: </span>            gameState = GameState.Paused;</pre>
<pre class="alt"><span class="lnum"> 33: </span>        }</pre>
<pre><span class="lnum"> 34: </span>    }</pre>
<pre class="alt"><span class="lnum"> 35: </span>&nbsp;</pre>
<pre><span class="lnum"> 36: </span>    <span class="rem">//check the resumebutton</span></pre>
<pre class="alt"><span class="lnum"> 37: </span>    <span class="kwrd">if</span> (gameState == GameState.Paused)</pre>
<pre><span class="lnum"> 38: </span>    {</pre>
<pre class="alt"><span class="lnum"> 39: </span>        Rectangle resumeButtonRect = <span class="kwrd">new</span> Rectangle((<span class="kwrd">int</span>)resumeButtonPosition.X, </pre>
<pre><span class="lnum"> 40: </span>                                                (<span class="kwrd">int</span>)resumeButtonPosition.Y, 100, 20);</pre>
<pre class="alt"><span class="lnum"> 41: </span>&nbsp;</pre>
<pre><span class="lnum"> 42: </span>        <span class="kwrd">if</span> (mouseClickRect.Intersects(resumeButtonRect))</pre>
<pre class="alt"><span class="lnum"> 43: </span>        {</pre>
<pre><span class="lnum"> 44: </span>            gameState = GameState.Playing;</pre>
<pre class="alt"><span class="lnum"> 45: </span>        }</pre>
<pre><span class="lnum"> 46: </span>    }</pre>
<pre class="alt"><span class="lnum"> 47: </span>}</pre>
</div>
<p>And that&rsquo;s about it, we&rsquo;ve just created a game that uses states to determine if it should pause, play or show the main menu. For my complete solution you can click the link below and feel free to post comments if you have any questions.</p>
<p>Thank you for reading!</p>
<p><a href="https://skydrive.live.com/#!/?cid=5a345bb15e973473&amp;sc=documents&amp;uc=1&amp;id=5A345BB15E973473%21250" target="_blank">Source Code</a></p>
{% include imported_disclaimer.html %}
