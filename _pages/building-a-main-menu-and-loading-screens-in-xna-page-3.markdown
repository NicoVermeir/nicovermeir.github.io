---
layout: post
title: "Building a main menu and loading screens in XNA–Page 3"
date: 2011-07-14 15:11:36 +0200
comments: false
author: Nico Vermeir
sitemap: false
permalink: /building-a-main-menu-and-loading-screens-in-xna-page-3.html
redirect_from:
 - /blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-3.aspx.html
 - /blog/page/building-a-main-menu-and-loading-screens-in-xna-page-3.aspx.html
---
<ul>
<li><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA.aspx">Page 1</a></li>
<li><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA&ndash;Page-2.aspx">Page 2</a></li>
</ul>
<p>Finally the part why we&rsquo;re all here, adding a main menu in an XNA game.</p>
<p>First we need to remove the call to the LoadGame() method in the Initialize() method and set an initial game state. Change the Initialize method so it looks like this:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">protected</span> <span class="kwrd">override</span> <span class="kwrd">void</span> Initialize()</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    <span class="rem">//enable the mousepointer</span></pre>
<pre><span class="lnum"> 4: </span>    IsMouseVisible = <span class="kwrd">true</span>;</pre>
<pre class="alt"><span class="lnum"> 5: </span>&nbsp;</pre>
<pre><span class="lnum"> 6: </span>    <span class="rem">//set the position of the buttons</span></pre>
<pre class="alt"><span class="lnum"> 7: </span>    startButtonPosition = <span class="kwrd">new</span> Vector2((GraphicsDevice.Viewport.Width / 2) - 50, 200);</pre>
<pre><span class="lnum"> 8: </span>    exitButtonPosition = <span class="kwrd">new</span> Vector2((GraphicsDevice.Viewport.Width / 2) - 50, 250);</pre>
<pre class="alt"><span class="lnum"> 9: </span>&nbsp;</pre>
<pre><span class="lnum"> 10: </span>    <span class="rem">//set the gamestate to start menu</span></pre>
<pre class="alt"><span class="lnum"> 11: </span>    gameState = GameState.StartMenu;</pre>
<pre><span class="lnum"> 12: </span>    </pre>
<pre class="alt"><span class="lnum"> 13: </span>    <span class="kwrd">base</span>.Initialize();</pre>
<pre><span class="lnum"> 14: </span>}</pre>
</div>
<p>Next we need to load the textures for our two menu buttons, one button to start the game and one to exit the game. This is done in the LoadContent() method. Change it so it looks like this:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">protected</span> <span class="kwrd">override</span> <span class="kwrd">void</span> LoadContent()</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    <span class="rem">// Create a new SpriteBatch, which can be used to draw textures.</span></pre>
<pre><span class="lnum"> 4: </span>    spriteBatch = <span class="kwrd">new</span> <span class="kwrd">SpriteBatch</span>(GraphicsDevice);</pre>
<pre class="alt"><span class="lnum"> 5: </span>&nbsp;</pre>
<pre><span class="lnum"> 6: </span>    <span class="rem">//load the buttonimages into the content pipeline</span></pre>
<pre class="alt"><span class="lnum"> 7: </span>    startButton = <span class="kwrd">Content</span>.Load&lt;<span class="kwrd">Texture2D</span>&gt;(<span class="str">@"start"</span>);</pre>
<pre><span class="lnum"> 8: </span>    exitButton = <span class="kwrd">Content</span>.Load&lt;<span class="kwrd">Texture2D</span>&gt;(<span class="str">@"exit"</span>);</pre>
<pre class="alt"><span class="lnum"> 9: </span>}</pre>
</div>
<p>Off course we need to have the button images in our Content project. Save them here and add them to the project.</p>
<p align="center"><img src="http://i54.tinypic.com/o0t73n.jpg" alt="" /></p>
<p align="center"><img src="http://i55.tinypic.com/w8xj86.jpg" alt="" /></p>
<p>Now we need to draw the buttons in the Draw() method and this is where the magic happens.</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">protected</span> <span class="kwrd">override</span> <span class="kwrd">void</span> Draw(GameTime gameTime)</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    GraphicsDevice.Clear(Color.CornflowerBlue);</pre>
<pre><span class="lnum"> 4: </span>    spriteBatch.Begin();</pre>
<pre class="alt"><span class="lnum"> 5: </span>&nbsp;</pre>
<pre><span class="lnum"> 6: </span>    <span class="rem">//draw the start menu</span></pre>
<pre class="alt"><span class="lnum"> 7: </span>    <span class="kwrd">if</span> (gameState == GameState.StartMenu)</pre>
<pre><span class="lnum"> 8: </span>    {</pre>
<pre class="alt"><span class="lnum"> 9: </span>        spriteBatch.Draw(startButton, startButtonPosition, Color.White);</pre>
<pre><span class="lnum"> 10: </span>        spriteBatch.Draw(exitButton, exitButtonPosition, Color.White);</pre>
<pre class="alt"><span class="lnum"> 11: </span>    }</pre>
<pre><span class="lnum"> 12: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 13: </span>    <span class="rem">//draw the the game when playing</span></pre>
<pre><span class="lnum"> 14: </span>    <span class="kwrd">if</span> (gameState == GameState.Playing)</pre>
<pre class="alt"><span class="lnum"> 15: </span>    {</pre>
<pre><span class="lnum"> 16: </span>        <span class="rem">//orb</span></pre>
<pre class="alt"><span class="lnum"> 17: </span>        spriteBatch.Draw(orb, orbPosition, Color.White);</pre>
<pre><span class="lnum"> 18: </span>    }</pre>
<pre class="alt"><span class="lnum"> 19: </span>&nbsp;</pre>
<pre><span class="lnum"> 20: </span>    spriteBatch.End();</pre>
<pre class="alt"><span class="lnum"> 21: </span>    <span class="kwrd">base</span>.Draw(gameTime);</pre>
<pre><span class="lnum"> 22: </span>}</pre>
</div>
<p>By checking the game&rsquo;s state we can determine what to draw. The if construction on line 7 will be entered when the game launches because we specified that state in the Initialize() method, later when we click the start button the state will change to Playing and the if on line 14 will be executed.</p>
<p>If we launch the game now we&rsquo;ll see the menu but the buttons don&rsquo;t work yet. First we&rsquo;ll need to add a new method that&rsquo;ll handle the click events. Create this method:</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="kwrd">void</span> MouseClicked(<span class="kwrd">int</span> x, <span class="kwrd">int</span> y)</pre>
<pre><span class="lnum"> 2: </span>{</pre>
<pre class="alt"><span class="lnum"> 3: </span>    <span class="rem">//creates a rectangle of 10x10 around the place where the mouse was clicked</span></pre>
<pre><span class="lnum"> 4: </span>    Rectangle mouseClickRect = <span class="kwrd">new</span> Rectangle(x, y, 10, 10);</pre>
<pre class="alt"><span class="lnum"> 5: </span>&nbsp;</pre>
<pre><span class="lnum"> 6: </span>    <span class="rem">//check the startmenu</span></pre>
<pre class="alt"><span class="lnum"> 7: </span>    <span class="kwrd">if</span> (gameState == GameState.StartMenu)</pre>
<pre><span class="lnum"> 8: </span>    {</pre>
<pre class="alt"><span class="lnum"> 9: </span>        Rectangle startButtonRect = <span class="kwrd">new</span> Rectangle((<span class="kwrd">int</span>)startButtonPosition.X,</pre>
<pre><span class="lnum"> 10: </span>                                    (<span class="kwrd">int</span>)startButtonPosition.Y, 100, 20);</pre>
<pre class="alt"><span class="lnum"> 11: </span>        Rectangle exitButtonRect = <span class="kwrd">new</span> Rectangle((<span class="kwrd">int</span>)exitButtonPosition.X, </pre>
<pre><span class="lnum"> 12: </span>                                    (<span class="kwrd">int</span>)exitButtonPosition.Y, 100, 20);</pre>
<pre class="alt"><span class="lnum"> 13: </span>&nbsp;</pre>
<pre><span class="lnum"> 14: </span>        <span class="kwrd">if</span> (mouseClickRect.Intersects(startButtonRect)) <span class="rem">//player clicked start button</span></pre>
<pre class="alt"><span class="lnum"> 15: </span>        {</pre>
<pre><span class="lnum"> 16: </span>            <span class="rem">//gameState = GameState.Loading;</span></pre>
<pre class="alt"><span class="lnum"> 17: </span>            gameState = GameState.Playing;</pre>
<pre><span class="lnum"> 18: </span>            isLoading = <span class="kwrd">true</span>;</pre>
<pre class="alt"><span class="lnum"> 19: </span>        }</pre>
<pre><span class="lnum"> 20: </span>        <span class="kwrd">else</span> <span class="kwrd">if</span> (mouseClickRect.Intersects(exitButtonRect)) <span class="rem">//player clicked exit button</span></pre>
<pre class="alt"><span class="lnum"> 21: </span>        {</pre>
<pre><span class="lnum"> 22: </span>            Exit();</pre>
<pre class="alt"><span class="lnum"> 23: </span>        }</pre>
<pre><span class="lnum"> 24: </span>    }</pre>
<pre class="alt"><span class="lnum"> 25: </span>}</pre>
</div>
<p>So what this does is putting a rectangle around the two buttons and putting a rectangle around the point where the mouse clicked. Then the method checks if those two rectangles intersect, if they do a button was clicked.</p>
<p>Next we need to update the Update() method again so we can check there if the mouse was clicked. Add this code right above base.Update(GameTime) in the Update() method</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="rem">//wait for mouseclick</span></pre>
<pre><span class="lnum"> 2: </span>mouseState = Mouse.GetState();</pre>
<pre class="alt"><span class="lnum"> 3: </span><span class="kwrd">if</span> (previousMouseState.LeftButton == ButtonState.Pressed &amp;&amp; </pre>
<pre><span class="lnum"> 4: </span>    mouseState.LeftButton == ButtonState.Released)</pre>
<pre class="alt"><span class="lnum"> 5: </span>{</pre>
<pre><span class="lnum"> 6: </span>    MouseClicked(mouseState.X, mouseState.Y);</pre>
<pre class="alt"><span class="lnum"> 7: </span>}</pre>
<pre><span class="lnum"> 8: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 9: </span>previousMouseState = mouseState;</pre>
<pre><span class="lnum"> 10: </span>&nbsp;</pre>
<pre class="alt"><span class="lnum"> 11: </span><span class="kwrd">if</span> (gameState == GameState.Playing &amp;&amp; isLoading)</pre>
<pre><span class="lnum"> 12: </span>{</pre>
<pre class="alt"><span class="lnum"> 13: </span>    LoadGame();</pre>
<pre><span class="lnum"> 14: </span>    isLoading = <span class="kwrd">false</span>;</pre>
<pre class="alt"><span class="lnum"> 15: </span>}</pre>
</div>
<p>The if construction on line 3 will be entered when the previous mouse state was pressed and the current is released. The if construction on line 11 will be entered when we need to load the orb texture. Before we can test this we need the previousMouseState to have an initial value. Add this code in the Initialize() method right above base.Initialize():</p>
<div class="csharpcode">
<pre class="alt"><span class="lnum"> 1: </span><span class="rem">//get the mouse state</span></pre>
<pre><span class="lnum"> 2: </span>mouseState = Mouse.GetState();</pre>
<pre class="alt"><span class="lnum"> 3: </span>previousMouseState = mouseState;</pre>
</div>
<p>When we launch the game and click the start button the orb will start moving and our implementation of a main menu is complete.</p>
<p><a href="http://www.spikie.be/blog/page/Building-a-main-menu-and-loading-screens-in-XNA-Page-4.aspx" target="_top">On the next page we will introduce the loading screen, so let&rsquo;s get to it! &ndash;&gt;</a></p>
{% include imported_disclaimer.html %}
