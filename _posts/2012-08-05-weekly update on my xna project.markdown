---
layout: post
title: "weekly update on my xna project"
date: 2012-08-05 21:09:11 +0200
comments: true
published: true
categories: ["post"]
tags: [".Net", "Game development", "WP7", "XNA"]
alias: ["/blog/post/2012/08/05/Weekly-update-on-my-XNA-project.aspx", "/blog/post/2012/08/05/weekly-update-on-my-xna-project.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2012/08/05/Weekly-update-on-my-XNA-project.aspx
 - /blog/post/2012/08/05/weekly-update-on-my-xna-project.aspx
---
<p>My first week back on the job is finished, that means I’ve had about 5 hours of time to work on my lunch game. Progress started of pretty small because of some enemy placement issues that showed up after I changed the complete control scheme. After loosing about an hour of time trying to fix it, I’ve decided to revert back to the virtual thumbsticks. About half an hour and some Mercurial commands later I was back to the original controls, the enemy movement behavior also reverted back to normal.</p>  <p>Day 2 I decided it was about time to make the enemy move, the character itself was moving up and down but without any animation, he was just sliding up and down. My animations are done using a spritesheet, the eight walking directions each have a line in the spritesheet. In the code itself I have a rectangle that has the width and height of one texture on the sheet, using the Update method I move the rectangle after x amount of time. This is the piece of code that creates the animation</p>  <pre class="code"><span style="color: blue">protected </span><span style="color: #2b91af">Rectangle </span>SourceRectRunning;
<span style="color: blue">protected readonly </span><span style="color: #2b91af">TimeSpan </span>RunRate = <span style="color: #2b91af">TimeSpan</span>.FromSeconds(0.1);
<span style="color: blue">protected </span><span style="color: #2b91af">TimeSpan </span>RunTimer;

<span style="color: blue">public override void </span>Update(<span style="color: #2b91af">GameTime </span>gameTime)
{
    RunTimer -= gameTime.ElapsedGameTime;

    <span style="color: blue">if </span>(RunTimer &lt;= <span style="color: #2b91af">TimeSpan</span>.Zero)
    {
        <span style="color: blue">if </span>(CharState == <span style="color: #2b91af">State</span>.Running)
        {
            <span style="color: blue">if </span>(SourceRectRunning.X &gt;= TextureWidth * 7)
            {
                SourceRectRunning.X = 0;
                <span style="color: blue">return</span>;
            }

            SourceRectRunning.X = SourceRectRunning.X + (<span style="color: blue">int</span>)TextureWidth;
        }

        RunTimer = RunRate;
    }
}</pre>

<p>&#160;</p>

<p>The CharState is just en enum that I’ve build. The enum looks like this</p>

<pre class="code"><span style="color: blue">public enum </span><span style="color: #2b91af">State
</span>{
    Running,
    Standing,
    Spotted,
    Walking
}</pre>

<br />This makes it very easy for me to call the right code when the player starts moving or when he’s spotted by the enemy. 

<p>I was very happy of how the animation looked like, so I figured it was about time to do something about the collision detection. This is something I’ve implemented quite some time ago but it never really worked how it should. The way collision detection is working in my app is a pretty nifty one. I found it in the demo code of the tile engine that I’m using. </p>

<p>A map build with Tiled consist of several layers, I have a layer that contains only the floor textures and a second layer that contains stuff like trees, walls and other non-interactable objects. The demo project contained with the WP7 Tiled engine has a method that can get the color of a certain tile on a certain layer. I take the player’s position, add a few pixels to it and determine the color on that location of the Objects layer, if the color is NULL then there’s no object in the way. Let me show you the code</p>

<pre class="code"><span style="color: #2b91af">Color</span>? collColor = _level.Map.GetColorAt(<span style="color: #a31515">&quot;Objects&quot;</span>, _player.WorldPosition + 
    (<span style="color: #2b91af">VirtualThumbsticks</span>.LeftThumbstick * 5));

<span style="color: blue">if </span>(collColor == <span style="color: blue">null</span>)
{
    <span style="color: green">//player can move
</span>}</pre>

<p>The GetColorAt method looks like this</p>

<pre class="code"><span style="color: blue">public </span><span style="color: #2b91af">Color</span>? GetColorAt(<span style="color: blue">string </span>layerName, <span style="color: #2b91af">Vector2 </span>position)
{
    <span style="color: blue">var </span>l = GetLayer(layerName);

    <span style="color: #2b91af">TileLayer </span>tileLayer = l <span style="color: blue">as </span><span style="color: #2b91af">TileLayer</span>;

    position.X = (<span style="color: blue">int</span>)position.X;
    position.Y = (<span style="color: blue">int</span>)position.Y;

    <span style="color: #2b91af">Vector2 </span>tilePosition = <span style="color: blue">new </span><span style="color: #2b91af">Vector2</span>((<span style="color: blue">int</span>)(position.X / TileWidth), (<span style="color: blue">int</span>)(position.Y/TileHeight));

    <span style="color: #2b91af">Tile </span>collisionTile = tileLayer.Tiles[(<span style="color: blue">int</span>)tilePosition.X, (<span style="color: blue">int</span>)tilePosition.Y];

    <span style="color: blue">if </span>(collisionTile == <span style="color: blue">null</span>)
        <span style="color: blue">return null</span>;

    <span style="color: blue">if </span>(collisionTile.CollisionData != <span style="color: blue">null</span>)
    {
        <span style="color: blue">int </span>positionOnTileX = ((<span style="color: blue">int</span>)position.X - (((<span style="color: blue">int</span>)position.X / TileWidth) * TileWidth));
        <span style="color: blue">int </span>positionOnTileY = ((<span style="color: blue">int</span>)position.Y - (((<span style="color: blue">int</span>)position.Y / TileHeight) * TileHeight));
        positionOnTileX = (<span style="color: blue">int</span>)<span style="color: #2b91af">MathHelper</span>.Clamp(positionOnTileX, 0, TileWidth);
        positionOnTileY = (<span style="color: blue">int</span>)<span style="color: #2b91af">MathHelper</span>.Clamp(positionOnTileY, 0, TileHeight);

        <span style="color: blue">int </span>pixelCheckX = (collisionTile.Source.X) + positionOnTileX;
        <span style="color: blue">int </span>pixelCheckY = (collisionTile.Source.Y) + positionOnTileY;

        <span style="color: blue">return </span>collisionTile.CollisionData[(pixelCheckY * collisionTile.Texture.Width) + pixelCheckX];
    }
    <span style="color: blue">else
    </span>{
        <span style="color: blue">return null</span>;
    }
}</pre>


<p>As I’ve said, this piece of code comes from the Tiled engine I’ve found on <a href="http://www.garethpw.co.uk/2010/09/phone-7-xna-tile-engine-base-solution.html" target="_blank">this site.</a></p>

<p>The problem I was having before was that I was passing the player’s coordinate on the screen to the method instead of the player’s position on the map. It does work much better now but currently I’m passing the coordinate of the upper left corner of the texture (XNA default) so it does need some fine-tuning.</p>

<p>I’m thinking next week to do some more work on the level editor or maybe start working on interactable objects. I’ll write another post next week, so stay tuned or follow me on Twitter to get daily updates.</p>
{% include imported_disclaimer.html %}
