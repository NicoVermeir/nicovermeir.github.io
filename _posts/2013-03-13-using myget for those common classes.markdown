---
layout: post
title: "using myget for those common classes"
date: 2013-03-13 13:00:00 +0100
comments: true
published: true
categories: ["post"]
tags: [".Net", "continuous delivery", "myget", "NuGet", "PCL"]
alias: ["/blog/post/2013/03/13/Using-MyGet-for-those-common-classes.aspx", "/blog/post/2013/03/13/using-myget-for-those-common-classes.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2013/03/13/Using-MyGet-for-those-common-classes.aspx
 - /blog/post/2013/03/13/using-myget-for-those-common-classes.aspx
---
<p>Every .net developer, be it a mobile, web or windows developer, uses (or should use) NuGet. A big, and fast growing, gallery of useful (<a href="http://nuget.org/packages/DoTheMario/" target="_blank">and less useful but fun</a>) libraries. The problem I was having with NuGet is that for every project I needed to go search in that big gallery for the packages I wanted. Luckily someone had the same problem and decided to do something about it. And so MyGet was born.</p>
<p><a href="http://www.myget.org/" target="_blank"><img style="float: none; margin-left: auto; display: block; margin-right: auto;" src="http://i50.tinypic.com/rjkxeu.jpg" alt="" /></a></p>
<p>The MyGet team sells MyGet as Nuget-as-a-service. You can create your own feed, drop in the NuGet packages you tend to use (even mirror them) and add the feed into your package manager. This way you get your own personal gallery, no more searching for the ones you need, those feeds are public by the way, everyone can use them. If you want a private feed you&rsquo;ll need to look at the MyGet price plans.</p>
<p>Getting started with MyGet is really easy, just go to <a href="http://www.myget.org">http://www.myget.org</a> log in with your Microsoft, Google, Yahoo, Facebook, OpenID, GitHub or StackOverflow account. After you login you can add feeds to your account.</p>
<p><a href="http://i45.tinypic.com/2n66oep.jpg" target="_blank"><img src="http://i45.tinypic.com/2n66oep.jpg" alt="" width="527" height="87" /></a></p>
<p>Creating a feed is as easy as giving it a name and a description and selecting the feed type (public, community or private). Once your feed is created you can start adding packages to it. To add a package enter the search term just like you would do when you use NuGet in Visual Studio, click the package you want and click Add.</p>
<p><a href="http://i45.tinypic.com/dqnmmb.jpg" target="_blank"><img src="http://i45.tinypic.com/dqnmmb.jpg" alt="" width="304" height="273" /></a></p>
<p>If you want, in the Add package dialog, you can select to mirror the package. This will copy the entire package from the NuGet feed into MyGet, meaning that you&rsquo;ll still be able to install the package when NuGet goes down. To give you an idea of how a feed could look like, here&rsquo;s some of the packages in my Windows Phone feed.</p>
<p><a href="http://i47.tinypic.com/2ltocw0.jpg" target="_blank"><img src="http://i47.tinypic.com/2ltocw0.jpg" alt="" width="310" height="348" /></a></p>
<p>To use your freshly created feed you&rsquo;ll need to add it as a package source in NuGet. Go to the Package Manager settings from the Visual Studio options menu (or open the Manage NuGet packages dialog in a project and click Settings). Here you can add a new package source.</p>
<p><a href="http://i45.tinypic.com/23r1mrs.jpg" target="_blank"><img src="http://i45.tinypic.com/23r1mrs.jpg" alt="" width="536" height="312" /></a></p>
<p>Once added, the new package source will show up in NuGet and you&rsquo;ll be able to install the packages from you own feed.</p>
<p><a href="http://i45.tinypic.com/jg1sfl.jpg" target="_blank"><img src="http://i45.tinypic.com/jg1sfl.jpg" alt="" width="542" height="361" /></a></p>
<p>Managing my own package feed has really reduced the time I used to spent searching and adding packages. But the MyGet team has been working hard on a really cool feature.</p>
<h2>Do the Wonka</h2>
<p>The MyGet team (all Belgian developers by the way &ndash; hooray for Belgium <img class="wlEmoticon wlEmoticon-smile" style="border-style: none;" src="http://www.spikie.be/blog/images/wlEmoticon-smile_15.png" alt="Smile" />) developed a feature they codenamed &ldquo;Wonka&rdquo;. It&rsquo;s currently in Beta but it&rsquo;s working great so far. This is what Wonka is all about</p>
<p><img src="http://i48.tinypic.com/63ya7q.jpg" alt="" /></p>
<p>They&rsquo;ve setup their own Build server, including support for building Windows Phone 8 libraries (thanks to the command line compiler in the WP8 SDK). Hearing about such a cool feature and hearing that it&rsquo;s in beta made me want to try it. And I started thinking, maybe I could put a bunch of classes that I tend to use in some form in multiple projects in one library and have that library in my feed.</p>
<p>The project itself is still very small but it will grow when I need extra functionality, it currently holds a Regex class to get rid of HTML tags in strings and a RestClient class that uses the portable HttpClient to do a Get operation. For reference, here are the classes.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:7cb6aad4-69e5-4399-9dfd-d938508e7e0f" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">static</span><span style="background: #ffffff; color: #0000ff;">class</span><span style="background: #ffffff; color: #2b91af;">RegexHelper</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #2b91af;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">static</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> RemoveHtmlTags(</span><span style="background: #ffffff; color: #0000ff;">this</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> text)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//add some layout</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">text = </span><span style="background: #ffffff; color: #2b91af;">Regex</span><span style="background: #ffffff; color: #000000;">.Replace(text, </span><span style="background: #ffffff; color: #a31515;">"&lt;br /&gt;"</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #a31515;">"\n"</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">text = </span><span style="background: #ffffff; color: #2b91af;">Regex</span><span style="background: #ffffff; color: #000000;">.Replace(text, </span><span style="background: #ffffff; color: #a31515;">"&lt;/p&gt;"</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #a31515;">"\n"</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//replace weird characters</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">text = </span><span style="background: #ffffff; color: #2b91af;">Regex</span><span style="background: #ffffff; color: #000000;">.Replace(text, </span><span style="background: #ffffff; color: #a31515;">"&amp;amp;"</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #a31515;">"&amp;"</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">text = </span><span style="background: #ffffff; color: #2b91af;">Regex</span><span style="background: #ffffff; color: #000000;">.Replace(text, </span><span style="background: #ffffff; color: #a31515;">"&amp;nbsp;"</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #a31515;">" "</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//remove remaining HTML tags</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #2b91af;">Regex</span><span style="background: #ffffff; color: #000000;">.Replace(text, </span><span style="background: #ffffff; color: #a31515;">@"&lt;[^&gt;]*&gt;"</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">.Empty);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:32457e6a-44ec-47fc-8d3e-026646d9d367" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">class</span><span style="background: #ffffff; color: #2b91af;">RestClient</span><span style="background: #ffffff; color: #000000;"> : </span><span style="background: #ffffff; color: #2b91af;">IRestClient</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #2b91af;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #2b91af;">HttpClient</span><span style="background: #ffffff; color: #000000;"> _client;</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">async</span><span style="background: #ffffff; color: #2b91af;">Task</span><span style="background: #ffffff; color: #000000;">&lt;T&gt; Get&lt;T&gt;(CancellationToken cancellationToken, </span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> url, </span><span style="background: #ffffff; color: #0000ff;">bool</span><span style="background: #ffffff; color: #000000;"> includeResultProps = </span><span style="background: #ffffff; color: #0000ff;">true</span><span style="background: #ffffff; color: #000000;">)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">where</span><span style="background: #ffffff; color: #000000;"> T : </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #000000;">()</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">try</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_client = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">HttpClient</span><span style="background: #ffffff; color: #000000;">();</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> result = </span><span style="background: #ffffff; color: #0000ff;">await</span><span style="background: #ffffff; color: #000000;"> _client.GetAsync(url);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> json = </span><span style="background: #ffffff; color: #0000ff;">await</span><span style="background: #ffffff; color: #000000;"> result.Content.ReadAsStringAsync();</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (!includeResultProps)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">JObject</span><span style="background: #ffffff; color: #000000;"> obj = </span><span style="background: #ffffff; color: #2b91af;">JObject</span><span style="background: #ffffff; color: #000000;">.Parse(json);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">json = obj[</span><span style="background: #ffffff; color: #a31515;">"results"</span><span style="background: #ffffff; color: #000000;">].ToString();</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #2b91af;">JsonConvert</span><span style="background: #ffffff; color: #000000;">.DeserializeObject&lt;T&gt;(json);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">catch</span><span style="background: #ffffff; color: #000000;"> (</span><span style="background: #ffffff; color: #2b91af;">JsonSerializationException</span><span style="background: #ffffff; color: #000000;"> e)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #000000;"> T();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>The project contains Json.net and the portable HttpClient, both referenced through NuGet. Now we need a way for the build server to get a hold of these packages, we can accomplish this by enabling the NuGet package restore. Right-click the solution and you&rsquo;ll see the option right there.</p>
<p><a href="http://i46.tinypic.com/166jckj.jpg" target="_blank"><img src="http://i46.tinypic.com/166jckj.jpg" alt="" /></a></p>
<p>This will add a .nuget folder to your solution that you&rsquo;ll need to check-in into your source control.</p>
<p>So we have a working project with some classes that we would like to get into a NuGet package by using the MyGet build server. What we need to do is get this package into a repository. I used BitBucket for this because it has free private repositories. You can also add a GitHub or Codeplex repository.</p>
<p><a href="http://i45.tinypic.com/2zsbng5.jpg" target="_blank"><img src="http://i45.tinypic.com/2zsbng5.jpg" alt="" width="494" height="143" /></a></p>
<p>If you use the from BitBucket button it will ask you for permission to access your account on BitBucket and will show you a list of all your repositories hosted there. Select the one you need and click Add. Do take notice of the warning.</p>
<p><a href="http://i50.tinypic.com/2lve4j5.jpg" target="_blank"><img src="http://i50.tinypic.com/2lve4j5.jpg" alt="" width="504" height="142" /></a></p>
<p>Add the repository and click the edit button next to it. Enter your BitBucket credentials here so MyGet can access your private repository to fetch the sources. And that&rsquo;s it, you can now hit the Build button to fetch the sources and try to build them. In my case this immediately resulted in a big fat red Failed message. The problem here is something of a chicken-egg problem (a big thanks here to the MyGet team for helping me figuring this one out!). So here&rsquo;s the problem:</p>
<ul>
<li>The project file needs the package to run</li>
<li>The project file is needed for package restore</li>
<li>package needs package restore</li>
</ul>
<p>The solution here is to setup a build.bat in the solution folder. The MyGet build server will search for a build.bat to execute, if it doesn&rsquo;t find one it will just use the sln available.</p>
<p>With the build.bat file we first fetch the necessary packages and then build the solution</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:e694d299-430a-49f0-9177-0e43622cfa67" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #000000;">@echo Off</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">set config=%1</span></li>
<li><span style="background: #ffffff; color: #000000;">if "%config%" == "" (</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">set config=Release</span></li>
<li><span style="background: #ffffff; color: #000000;">)</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li><span style="background: #ffffff; color: #000000;">set version=</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">if not "%PackageVersion%" == "" (</span></li>
<li>&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">set version=-Version %PackageVersion%</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">)</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">REM Run package restore</span></li>
<li><span style="background: #ffffff; color: #000000;">%nuget% install WindowsPhone\packages.config -OutputDirectory packages -Prerelease -NonInteractive</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li><span style="background: #ffffff; color: #000000;">REM Build solution</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">%WINDIR%\Microsoft.NET\Framework\v4.0.30319\msbuild WindowsPhone.sln /p:Configuration="%config%" /m /v:M /fl /flp:LogFile=msbuild.log;Verbosity=Normal /nr:false</span></li>
</ol></div>
</div>
</div>
<p>Add this to your solution in Visual Studio, make sure it&rsquo;s called build.bat and check it into your source control. Now it should build.</p>
<h2>Automating builds</h2>
<p>Next step to make everything even more awesome is to add a hook to BitBucket so that MyGet will fetch and build the sources everytime a commit is pushed into the repository. First you&rsquo;ll need the HTTP POST link from MyGet, you&rsquo;ll find it underneath your build source.</p>
<p><a href="http://i48.tinypic.com/2j4a88j.jpg" target="_blank"><img src="http://i48.tinypic.com/2j4a88j.jpg" alt="" width="621" height="144" /></a></p>
<p>Once you&rsquo;ve got that link, go to the BitBucket repository and click the Settings icon, in the Settings go to Services and add a POST service.</p>
<p><a href="http://i47.tinypic.com/124geiv.jpg" target="_blank"><img src="http://i47.tinypic.com/124geiv.jpg" alt="" width="390" height="140" /></a></p>
<p>Past the URL and save the service. Now everytime you push a commit to BitBucket a new build should get queued in MyGet (the page refreshes automatically to show the build process).</p>
<p><a href="http://i50.tinypic.com/2ecp7jq.jpg" target="_blank"><img src="http://i50.tinypic.com/2ecp7jq.jpg" alt="" width="392" height="107" /></a></p>
<p>And just like that we&rsquo;ve created a NuGet package in a MyGet feed with continuous delivery, how cool is this right? If I check my feed from Visual Studio my package shows up!</p>
<p><a href="http://i47.tinypic.com/zuoakm.jpg" target="_blank"><img src="http://i47.tinypic.com/zuoakm.jpg" alt="" width="404" height="269" /></a></p>
<h2>Conclusion</h2>
<p>In this post I&rsquo;ve explained how I use MyGet, a NuGet-as-a-service, to create a package feed with only the packages that are relevant for me. I&rsquo;ve also explained how I&rsquo;ve added my own library of classes that I use in multiple projects in that feed and setup continuous delivery so that the package gets updated with every commit that gets pushed to the repository.</p>
<p>Resources:</p>
<ul>
<li><a href="http://www.myget.org">http://www.myget.org</a></li>
<li><a title="http://blog.myget.org/" href="http://blog.myget.org/">http://blog.myget.org/</a></li>
</ul>
<p>A big shout out to the MyGet team for helping me getting started with their awesome service! <a title="http://www.myget.org/Home/Team" href="http://www.myget.org/Home/Team">http://www.myget.org/Home/Team</a></p>
{% include imported_disclaimer.html %}
