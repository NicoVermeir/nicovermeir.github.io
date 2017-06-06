---
layout: post
title: "copying nokia’s photobeamer with signalr and wp8"
date: 2013-01-21 09:13:00 +0100
comments: true
published: true
categories: ["post"]
tags: [".Net", "Metro", "signalr", "Web development", "WP8", "XAML"]
alias: ["/blog/post/2013/01/21/Copying-Nokias-Photobeamer-with-SignalR-and-WP8.aspx", "/blog/post/2013/01/21/copying-nokias-photobeamer-with-signalr-and-wp8.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2013/01/21/Copying-Nokias-Photobeamer-with-SignalR-and-WP8.aspx
 - /blog/post/2013/01/21/copying-nokias-photobeamer-with-signalr-and-wp8.aspx
---
<p>Some time ago Nokia launched a pretty impressive, almost magical app called &ldquo;Photobeamer&rdquo; (exclusive for the Nokia Lumia range). This is one of those apps that look very impressive and magical at first but when you start to think about it it&rsquo;s not really that hard to figure out how this works. I thought about it and decided to hack together a copy of this.</p>
<p>If you have no clue what Photobeamer is all about, check out this video from Pocketnow</p>
<p><iframe src="http://www.youtube.com/embed/fbvm1At-vGc" frameborder="0" width="560" height="315"></iframe></p>
<h2>Disclaimer</h2>
<p>Small remark about this video before we get started. The Nokia Photobeamer caches every image for about 30 days, meaning that if you select the same picture again it appears almost instantly, that&rsquo;s what happens in this video. We will get nowhere near that performance since my concept here doesn&rsquo;t do any caching. Also, they are streaming the image to the server (hence the fact that the picture is blurry at first and sharpens up later) we will just send over the entire image at once.</p>
<h2>Puzzle pieces</h2>
<p>All right, let&rsquo;s get started. We need a few pieces to complete this puzzle. We need a Windows Phone app, we&rsquo;ll need a service that takes care of sending the right picture to the right client and we&rsquo;ll need a web client to display the QR code and the picture.</p>
<p>This schematic shows the pieces and their roles</p>
<p><a href="http://i49.tinypic.com/2d0x7o2.jpg" target="_blank"><img src="http://i49.tinypic.com/2d0x7o2.jpg" alt="" width="324" height="243" /></a></p>
<p>The steps are:</p>
<ul>
<li>webclient generates unique ID (guid)</li>
<li>webclient registers a group on the server with that ID</li>
<li>webclients uses ID to generate QR code</li>
<li>user launches Windows Phone app</li>
<li>user selects picture</li>
<li>user scans QR code to get the generated ID</li>
<li>phone app joins the group with the ID</li>
<li>phone app deserializes the picture</li>
<li>phone app sends the picture to the other group members</li>
<li>webclient receives picture, serializes it again and shows it</li>
</ul>
<p>Lots of steps but it&rsquo;s easier to build as you might think. We&rsquo;ll start with the service as that&rsquo;s the glue to hold everything together.</p>
<h2>SignalR</h2>
<p>Since we need some form of real-time communication I&rsquo;ve decided to use SignalR. A snippet from the SignalR page that describes its functionality (<a href="http://signalr.net/" target="_blank">http://signalr.net/</a>):</p>

<blockquote>
<p>ASP.NET SignalR is a new library for ASP.NET developers that makes it incredibly simple to add real-time web functionality to your applications. What is "real-time web" functionality? It's the ability to have your server-side code push content to the connected clients as it happens, in real-time.</p>
<p>You may have heard of WebSockets, a new HTML5 API that enables bi-directional communication between the browser and server. SignalR will use WebSockets under the covers when it's available, and gracefully fallback to other techniques and technologies when it isn't, while your application code stays the same.</p>

</blockquote>

<p>All we need to get started is an empty web project and a nuget package use the package manager or the GUI</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:1b031bf5-24e1-45ec-a9ad-ac9f88d18b27" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li>Install-Package Microsoft.AspNet.SignalR -Pre</li>
</ol></div>
</div>
</div>
<p><a href="http://i49.tinypic.com/2dhb7lg.jpg" target="_blank"><img src="http://i49.tinypic.com/2dhb7lg.jpg" alt="" width="499" height="333" /></a></p>
<p>SignalR, at the time of writing, is in Release Candidate, so we need to include prerelease versions in Nuget or you won&rsquo;t find signalR at all. (although it&rsquo;s prerelease, SignalR is working really well so no worries)</p>
<p>Before we start building the service we need to add some method calls in Global.asax.cs in the Application_Start method.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:61783086-085f-4565-8951-a3716e93b66b" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> Application_Start(</span><span style="background: #ffffff; color: #0000ff;">object</span><span style="background: #ffffff; color: #000000;"> sender, </span><span style="background: #ffffff; color: #2b91af;">EventArgs</span><span style="background: #ffffff; color: #000000;"> e)</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">// Code that runs on application startup</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">AuthConfig</span><span style="background: #ffffff; color: #000000;">.RegisterOpenAuth();</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">GlobalHost</span><span style="background: #ffffff; color: #000000;">.HubPipeline.EnableAutoRejoiningGroups();</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">// Register the default hubs route: ~/signalr/hubs</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">RouteTable</span><span style="background: #ffffff; color: #000000;">.Routes.MapHubs();</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>The MapHubs() makes sure that the service calls are rerouted to the correct functions. The EnableAutoRejoiningGroups() is something that we need to do in this RC2 version but it will go away in RTM, see my <a href="http://stackoverflow.com/questions/14401825/signalr-javascript-client-callback-not-firing-when-using-clients-group" target="_blank">StackOverflow</a> question for more information.</p>
<p>SignalR uses the concept of Hubs, a hub is a class that contains all the service calls. Add a class called ImgHub to a folder called Hubs in the empty web project.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:623f2d76-debb-4182-91d9-d86751d9d03b" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">class</span><span style="background: #ffffff; color: #2b91af;">ImgHub</span><span style="background: #ffffff; color: #000000;"> : </span><span style="background: #ffffff; color: #2b91af;">Hub</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #2b91af;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> Create(</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> guid)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Groups.Add(Context.ConnectionId, guid);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> ShareImage(</span><span style="background: #ffffff; color: #0000ff;">byte</span><span style="background: #ffffff; color: #000000;">[] image, </span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> guid)</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Clients.OthersInGroup(guid).ReceiveImage(image);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> Leave(</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> guid)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Groups.Remove(Context.ConnectionId, guid);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>That&rsquo;s really not a lot of code for a service. First, every hub in a SignalR project needs to inherit from Microsoft.AspNet.SignalR.Hub. Next to that, every public method we declare in this class will be callable from the clients. First method is Create(), this takes in the unique ID that will be generated by the webclient in a minute. That ID is used to create a group. SignalR tries to add the connectionId from the client to a group with the guid as groupname. If that group doesn&rsquo;t exist yet it will create it. The second method is ShareImage. This will take in the deserialized image (as a byte array) and that same guid again. Now we need to send that byte array to other clients so we call Clients.OthersInGroup, other options are Clients.All, Clients.Groups, Clients.Others, Clients.Caller, Clients.AllExcept. Plenty of choices but OthersInGroup suits our needs the best. The OthersInGroup returns a dynamic object, we can attach anything we want here. We want to call ReceiveImage() on the client, that method isn&rsquo;t declared anywhere but since the object is dynamic the compiler won&rsquo;t give us any trouble. And finally there&rsquo;s a Leave() method allowing us to leave a group should it be needed.</p>
<p>The SignalR project can be hosted on any webhost that supports .net, for this demo I&rsquo;ve used Windows Azure Websites.</p>
<h2>Webclient</h2>
<p>1/3th done, the second part is the webclient. This is a normal website using asp.net webforms. It&rsquo;s responsible for generating the ID, passing it to SignalR to create the group, get a QR code and receive the image.</p>
<p>First thing we need is the project, second is a SignalR client and the SignalR Javascript library (yes we&rsquo;ll be needing Javascript and no I&rsquo;m not proud of this&hellip;)</p>
<p><a href="http://i48.tinypic.com/2ibp1qg.jpg" target="_blank"><img src="http://i48.tinypic.com/2ibp1qg.jpg" alt="" width="435" height="290" /></a> </p>
<p>First some HTML code, the Default.aspx page is nothing fancy, it will only show the QR code.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:a6e27b9c-1dac-47a7-92a9-bf514db49efe" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">body</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">form</span><span style="background: #ffffff; color: #ff0000;">id</span><span style="background: #ffffff; color: #0000ff;">="form1"</span><span style="background: #ffffff; color: #ff0000;">method</span><span style="background: #ffffff; color: #0000ff;">="post"</span><span style="background: #ffffff; color: #ff0000;">action</span><span style="background: #ffffff; color: #0000ff;">="ImagePage.aspx"&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">input</span><span style="background: #ffffff; color: #ff0000;">type</span><span style="background: #ffffff; color: #0000ff;">="hidden"</span><span style="background: #ffffff; color: #ff0000;">id</span><span style="background: #ffffff; color: #0000ff;">="hiddenByteArray"</span><span style="background: #ffffff; color: #ff0000;">name</span><span style="background: #ffffff; color: #0000ff;">="hiddenByteArray"</span><span style="background: #ffffff; color: #0000ff;">/&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">div</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">img</span><span style="background: #ffffff; color: #ff0000;">src</span><span style="background: #ffffff; color: #0000ff;">="/imagehandler.ashx"</span><span style="background: #ffffff; color: #ff0000;">style</span><span style="background: #ffffff; color: #0000ff;">="</span><span style="background: #ffffff; color: #ff0000;">text-align</span><span style="background: #ffffff; color: #000000;">: </span><span style="background: #ffffff; color: #0000ff;">center"</span><span style="background: #ffffff; color: #0000ff;">/&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #800000;">div</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #800000;">form</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #800000;">body</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
</ol></div>
</div>
</div>
<p>Notice the imagehandler.ashx reference? That&rsquo;s a generic handler that will take care of generating the QR code and passing it into this img tag. Generic handler is a file type that you can add through Visual Studio, here&rsquo;s the code for imagehandler.ashx.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:e1b17238-2076-48f9-addd-fedb0dbc5deb" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">class</span><span style="background: #ffffff; color: #2b91af;">ImageHandler</span><span style="background: #ffffff; color: #000000;"> : </span><span style="background: #ffffff; color: #2b91af;">IHttpHandler</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #2b91af;">IRequiresSessionState</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> ProcessRequest(</span><span style="background: #ffffff; color: #2b91af;">HttpContext</span><span style="background: #ffffff; color: #000000;"> context)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> guid = </span><span style="background: #ffffff; color: #2b91af;">HttpContext</span><span style="background: #ffffff; color: #000000;">.Current.Session[</span><span style="background: #ffffff; color: #a31515;">"InstanceGuid"</span><span style="background: #ffffff; color: #000000;">];</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> url = </span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">.Format(</span><span style="background: #ffffff; color: #a31515;">@"http://api.qrserver.com/v1/create-qr-code/?size=300x300&amp;data={</span><span style="background: #ffffff; color: #3cb371;">0}</span><span style="background: #ffffff; color: #a31515;">"</span><span style="background: #ffffff; color: #000000;">, guid);</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">WebClient</span><span style="background: #ffffff; color: #000000;"> client = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">WebClient</span><span style="background: #ffffff; color: #000000;">();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> bytes = client.DownloadData(</span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">Uri</span><span style="background: #ffffff; color: #000000;">(url));</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">context.Response.OutputStream.Write(bytes, 0, bytes.Length);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">context.Response.ContentType = </span><span style="background: #ffffff; color: #a31515;">"image/JPEG"</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">bool</span><span style="background: #ffffff; color: #000000;"> IsReusable</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">get</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #0000ff;">false</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>We&rsquo;re getting our guid from the session object (how it got there, we&rsquo;ll see in a minute) that&rsquo;s why we need the IRequiresSessionState interface. The ProcessRequest method we get from IHttpHandler and will get executed when the handler is called. So we first get the guid from the session, then we&rsquo;ll build a url to qpi.qrserver.com, an api that takes in a value and generates a QR code from that value. We use WebClient to get the data from that url, the byte array that we receive from it will be our generated QR code, we write it to the outputstream of the page&rsquo;s context and set the type to be an image/JPEG. There should be some error handling here if you&rsquo;re using this for production code (sometimes the qrserver API takes to long and times out).</p>
<p>Next, we&rsquo;ll have a look at Default.aspx.cs.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:afe4747d-110e-46fd-84a9-788a9f753c20" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">protected</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> Page_Load(</span><span style="background: #ffffff; color: #0000ff;">object</span><span style="background: #ffffff; color: #000000;"> sender, </span><span style="background: #ffffff; color: #2b91af;">EventArgs</span><span style="background: #ffffff; color: #000000;"> e)</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> guid = Guid.NewGuid();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">HttpContext</span><span style="background: #ffffff; color: #000000;">.Current.Session[</span><span style="background: #ffffff; color: #a31515;">"InstanceGuid"</span><span style="background: #ffffff; color: #000000;">] = guid.ToString();</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>Not much going on, we generate a new Guid and set it to the current session object.</p>
<p>Now, let&rsquo;s have a look at connection the webclient to the signalR server and getting ready to receive the image.</p>
<p><span style="text-decoration: underline;"><strong>Warning</strong>: the next part contains javascript code&hellip;</span></p>
<p>In Default.aspx we add this script</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:437188e7-0fc9-4e59-9309-d1d4f30ddf9f" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">script</span><span style="background: #ffffff; color: #ff0000;">src</span><span style="background: #ffffff; color: #0000ff;">="Scripts/jquery-1.7.1.min.js"&gt;&lt;/</span><span style="background: #ffffff; color: #800000;">script</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">script</span><span style="background: #ffffff; color: #ff0000;">src</span><span style="background: #ffffff; color: #0000ff;">="Scripts/jquery.signalR-1.0.0-rc2.min.js"&gt;&lt;/</span><span style="background: #ffffff; color: #800000;">script</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">script</span><span style="background: #ffffff; color: #ff0000;">src</span><span style="background: #ffffff; color: #0000ff;">="http://pbclone.azurewebsites.net/signalr/hubs/"</span><span style="background: #ffffff; color: #ff0000;">type</span><span style="background: #ffffff; color: #0000ff;">="text/javascript"&gt;&lt;/</span><span style="background: #ffffff; color: #800000;">script</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">script</span><span style="background: #ffffff; color: #ff0000;">type</span><span style="background: #ffffff; color: #0000ff;">="text/javascript"&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">$(</span><span style="background: #ffffff; color: #0000ff;">function</span><span style="background: #ffffff; color: #000000;"> () {</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">$.connection.hub.url = </span><span style="background: #ffffff; color: #a31515;">'http://pbclone.azurewebsites.net/signalr'</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//$.connection.hub.url = 'http://localhost:4341/signalr';</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">// Proxy created on the fly</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> mainHub = $.connection.imgHub;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> guid = </span><span style="background: #ffffff; color: #a31515;">'</span><span style="background: #ffff00; color: #000000;">&lt;%</span><span style="background: #ffffff; color: #0000ff;">=</span><span style="background: #ffffff; color: #2b91af;">HttpContext</span><span style="background: #ffffff; color: #000000;">.Current.Session[</span><span style="background: #ffffff; color: #a31515;">"InstanceGuid"</span><span style="background: #ffffff; color: #000000;">] </span><span style="background: #ffff00; color: #000000;">%&gt;</span><span style="background: #ffffff; color: #a31515;">'</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">// Declare a function on the hub so the server can invoke it</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">mainHub.client.receiveImage = </span><span style="background: #ffffff; color: #0000ff;">function</span><span style="background: #ffffff; color: #000000;"> (imageArray) {</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//window.location = "/ImagePage.aspx?arr=" + imageArray;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">$(</span><span style="background: #ffffff; color: #a31515;">'#hiddenByteArray'</span><span style="background: #ffffff; color: #000000;">).val(imageArray);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">$(</span><span style="background: #ffffff; color: #a31515;">'#form1'</span><span style="background: #ffffff; color: #000000;">).submit();</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">};</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">// Start the connection</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">$.connection.hub.start().done(</span><span style="background: #ffffff; color: #0000ff;">function</span><span style="background: #ffffff; color: #000000;"> () {</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">mainHub.server.create(guid);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">});</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">});</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #800000;">script</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
</ol></div>
</div>
</div>
<p>First, we need to add jquery and the SignalR javascript library to our page. The third included script comes from wherever you host your SignalR service. The /signalr/hubs/ will be a proxy in javascript that contains the methods in the hub, allowing us to use them from our clients (try browsing to that url and have a look inside the javascript). $.connection comes from the SignalR javascript library, we set the correct url and get the correct hub. We&rsquo;ll also use a bit of inline asp.net to get the guid from the session. Remember in the SignalR part that we called ReceiveImage on the dynamic object? Line 13 is where we declare a callback handler on that method call. We set the received value, which will be a byte array, to a hidden field and POST the form. Those handlers need to be set before we call the start() method on the hub. &lt;yourhub&gt;.client is where all your client side callbacks are registered. &lt;yourhub&gt;.server is where all server side methods can be called, those methods are loaded from the /signalr/hubs/ proxy. On line 20 we start the connection to the hub, once we&rsquo;re connected we&rsquo;ll call the create method and pass the guid in to create and join the group.</p>
<p>We&rsquo;ll need a second page in this webclient to actually show the image. Only one element on the page, an img element.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:206f1a21-e7ea-4f94-ada9-5cf4b04747a3" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">body</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">form</span><span style="background: #ffffff; color: #ff0000;">id</span><span style="background: #ffffff; color: #0000ff;">="form1"</span><span style="background: #ffffff; color: #ff0000;">runat</span><span style="background: #ffffff; color: #0000ff;">="server"&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">div</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #800000;">img</span><span style="background: #ffffff; color: #ff0000;">src</span><span style="background: #ffffff; color: #0000ff;">="/ByteArrayHandler.ashx"</span><span style="background: #ffffff; color: #ff0000;">style</span><span style="background: #ffffff; color: #0000ff;">="</span><span style="background: #ffffff; color: #ff0000;">text-align</span><span style="background: #ffffff; color: #000000;">: </span><span style="background: #ffffff; color: #0000ff;">center"</span><span style="background: #ffffff; color: #0000ff;">/&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #800000;">div</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #800000;">form</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li><span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #800000;">body</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
</ol></div>
</div>
</div>
<p>The img element uses a second generic handler that will take care of deserializing the byte array back into an image.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:3b8614ea-16f4-4aea-b558-8526764ce812" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">class</span><span style="background: #ffffff; color: #2b91af;">ByteArrayHandler</span><span style="background: #ffffff; color: #000000;"> : </span><span style="background: #ffffff; color: #2b91af;">IHttpHandler</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #2b91af;">IRequiresSessionState</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> ProcessRequest(</span><span style="background: #ffffff; color: #2b91af;">HttpContext</span><span style="background: #ffffff; color: #000000;"> context)</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> base64String = </span><span style="background: #ffffff; color: #2b91af;">HttpContext</span><span style="background: #ffffff; color: #000000;">.Current.Session[</span><span style="background: #ffffff; color: #a31515;">"ByteArray"</span><span style="background: #ffffff; color: #000000;">].ToString();</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">byte</span><span style="background: #ffffff; color: #000000;">[] convertedFromBase64 = </span><span style="background: #ffffff; color: #2b91af;">Convert</span><span style="background: #ffffff; color: #000000;">.FromBase64String(base64String);</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">context.Response.OutputStream.Write(convertedFromBase64, 0, convertedFromBase64.Length);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">context.Response.ContentType = </span><span style="background: #ffffff; color: #a31515;">"image/JPEG"</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">bool</span><span style="background: #ffffff; color: #000000;"> IsReusable</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">get</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #0000ff;">false</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>We&rsquo;ll get the byte array from the session object. SignalR encodes arrays with Base64 encoding so we need to decode that first, once that&rsquo;s done we can just write the byte array into the outputstream as a JPEG, just like we did with the QR code. Onto the ImagePage.aspx.cs to see how the byte array goes into the session object</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:0695fe65-149f-4c87-89df-f08daa87df8f" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">protected</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> Page_Load(</span><span style="background: #ffffff; color: #0000ff;">object</span><span style="background: #ffffff; color: #000000;"> sender, </span><span style="background: #ffffff; color: #2b91af;">EventArgs</span><span style="background: #ffffff; color: #000000;"> e)</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">NameValueCollection</span><span style="background: #ffffff; color: #000000;"> postedValues = Request.Form;</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> base64String = postedValues[</span><span style="background: #ffffff; color: #a31515;">"hiddenByteArray"</span><span style="background: #ffffff; color: #000000;">];</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">HttpContext</span><span style="background: #ffffff; color: #000000;">.Current.Session[</span><span style="background: #ffffff; color: #a31515;">"ByteArray"</span><span style="background: #ffffff; color: #000000;">] = base64String;</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>We get our POST values from Request.Form, look for the hidden field called hiddenByteArray and place its value in the session.</p>
<p>We now have our service and one client complete, all that&rsquo;s left is building a Windows Phone application that connects to that same group on that same service and send the picture over.</p>
<h2>Windows Phone client</h2>
<p>For this we&rsquo;ll need a Windows Phone 8 project as SignalR has no official support for Windows Phone 7. Make sure that you have the latest version of Nuget installed or it won&rsquo;t find the correct SignalR assembly for Windows Phone 8 (thanks David Fowler for pointing this out).</p>
<p><a href="http://i46.tinypic.com/2mpe915.jpg" target="_blank"><img src="http://i46.tinypic.com/2mpe915.jpg" alt="" width="508" height="339" /></a></p>
<p>Add the SignalR.Client assembly to the project. Here&rsquo;s the XAML for the MainPage.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:fa144744-bb81-4c5a-a88c-d2d3b8c95255" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">StackPanel</span><span style="background: #ffffff; color: #ff0000;"> x</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #ff0000;">Name</span><span style="background: #ffffff; color: #0000ff;">="TitlePanel"</span><span style="background: #ffffff; color: #ff0000;"> Grid.Row</span><span style="background: #ffffff; color: #0000ff;">="0"</span><span style="background: #ffffff; color: #ff0000;"> Margin</span><span style="background: #ffffff; color: #0000ff;">="12,17,0,28"&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">TextBlock</span><span style="background: #ffffff; color: #ff0000;"> Text</span><span style="background: #ffffff; color: #0000ff;">="MY APPLICATION"</span><span style="background: #ffffff; color: #ff0000;"> Style</span><span style="background: #ffffff; color: #0000ff;">="{</span><span style="background: #ffffff; color: #a31515;">StaticResource</span><span style="background: #ffffff; color: #ff0000;"> PhoneTextNormalStyle}</span><span style="background: #ffffff; color: #0000ff;">"</span><span style="background: #ffffff; color: #ff0000;"> Margin</span><span style="background: #ffffff; color: #0000ff;">="12,0"/&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">TextBlock</span><span style="background: #ffffff; color: #ff0000;"> x</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #ff0000;">Name</span><span style="background: #ffffff; color: #0000ff;">="StatusText"</span><span style="background: #ffffff; color: #ff0000;"> Text</span><span style="background: #ffffff; color: #0000ff;">="Not connected"</span><span style="background: #ffffff; color: #ff0000;"> Margin</span><span style="background: #ffffff; color: #0000ff;">="9,-7,0,0"</span><span style="background: #ffffff; color: #ff0000;"> Style</span><span style="background: #ffffff; color: #0000ff;">="{</span><span style="background: #ffffff; color: #a31515;">StaticResource</span><span style="background: #ffffff; color: #ff0000;"> PhoneTextTitle1Style}</span><span style="background: #ffffff; color: #0000ff;">"/&gt;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #a31515;">StackPanel</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #008000;">&lt;!--ContentPanel - place additional content here--&gt;</span></li>
<li><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">Grid</span><span style="background: #ffffff; color: #ff0000;"> x</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #ff0000;">Name</span><span style="background: #ffffff; color: #0000ff;">="ContentPanel"</span><span style="background: #ffffff; color: #ff0000;"> Grid.Row</span><span style="background: #ffffff; color: #0000ff;">="1"</span><span style="background: #ffffff; color: #ff0000;"> Margin</span><span style="background: #ffffff; color: #0000ff;">="12,0,12,0"&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">Rectangle</span><span style="background: #ffffff; color: #ff0000;"> x</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #ff0000;">Name</span><span style="background: #ffffff; color: #0000ff;">="_previewRect"</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #ff0000;"> Margin</span><span style="background: #ffffff; color: #0000ff;">="0"</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #ff0000;"> Height</span><span style="background: #ffffff; color: #0000ff;">="800"</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #ff0000;"> Width</span><span style="background: #ffffff; color: #0000ff;">="600"</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #ff0000;"> HorizontalAlignment</span><span style="background: #ffffff; color: #0000ff;">="Center"</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #ff0000;"> VerticalAlignment</span><span style="background: #ffffff; color: #0000ff;">="Center"&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">Rectangle.Fill</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">VideoBrush</span><span style="background: #ffffff; color: #ff0000;"> x</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #ff0000;">Name</span><span style="background: #ffffff; color: #0000ff;">="_previewVideo"&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">VideoBrush.RelativeTransform</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li>&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</span><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">CompositeTransform</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #ff0000;"> x</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #ff0000;">Name</span><span style="background: #ffffff; color: #0000ff;">="_previewTransform"</span><span style="background: #ffffff; color: #ff0000;"> CenterX</span><span style="background: #ffffff; color: #0000ff;">=".5"</span><span style="background: #ffffff; color: #ff0000;"> CenterY</span><span style="background: #ffffff; color: #0000ff;">=".5" /&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #a31515;">VideoBrush.RelativeTransform</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #a31515;">VideoBrush</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #a31515;">Rectangle.Fill</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #a31515;">Rectangle</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">ListBox</span><span style="background: #ffffff; color: #ff0000;"> Margin</span><span style="background: #ffffff; color: #0000ff;">="10"</span><span style="background: #ffffff; color: #ff0000;"> x</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #ff0000;">Name</span><span style="background: #ffffff; color: #0000ff;">="_matchesList"</span><span style="background: #ffffff; color: #ff0000;"> FontSize</span><span style="background: #ffffff; color: #0000ff;">="30"</span><span style="background: #ffffff; color: #ff0000;"> FontWeight</span><span style="background: #ffffff; color: #0000ff;">="ExtraBold" /&gt;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #a31515;">Grid</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
</ol></div>
</div>
</div>
<p>I use one of the default textboxes in the page&rsquo;s header for a connection status. In the content grid we place a rectangle filled with a VideoBrush, this will be used to scan the QR code, Let&rsquo;s have a look at the code for the WP app.</p>
<p>First we declare some fields</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:d2ccf617-a71f-4ba0-a9e3-068a4559558d" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> _guid;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #2b91af;">PhotoChooserTask</span><span style="background: #ffffff; color: #000000;"> _photoChooserTask;</span></li>
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #2b91af;">IHubProxy</span><span style="background: #ffffff; color: #000000;"> _mainHub;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #2b91af;">HubConnection</span><span style="background: #ffffff; color: #000000;"> _connection;</span></li>
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">readonly</span><span style="background: #ffffff; color: #2b91af;">DispatcherTimer</span><span style="background: #ffffff; color: #000000;"> _timer;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #2b91af;">PhotoCameraLuminanceSource</span><span style="background: #ffffff; color: #000000;"> _luminance;</span></li>
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #2b91af;">QRCodeReader</span><span style="background: #ffffff; color: #000000;"> _reader;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #2b91af;">PhotoCamera</span><span style="background: #ffffff; color: #000000;"> _photoCamera;</span></li>
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #2b91af;">Stream</span><span style="background: #ffffff; color: #000000;"> _imgStream;</span></li>
</ol></div>
</div>
</div>
<p>I&rsquo;ll explain these when we encounter them. Now for the page&rsquo;s constructor</p>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:265e4358-8d64-4dfd-ad46-b9f421a19cfb" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #000000;"> MainPage()</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">InitializeComponent();</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_photoCamera = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">PhotoCamera</span><span style="background: #ffffff; color: #000000;">();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_photoCamera.Initialized += OnPhotoCameraInitialized;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_previewVideo.SetSource(_photoCamera);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">CameraButtons</span><span style="background: #ffffff; color: #000000;">.ShutterKeyHalfPressed += (sender, args) =&gt; _photoCamera.Focus();</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_photoChooserTask = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">PhotoChooserTask</span><span style="background: #ffffff; color: #000000;">();</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_photoChooserTask.Completed += photoChooserTask_Completed;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_photoChooserTask.Show();</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_timer = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">DispatcherTimer</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Interval = TimeSpan.FromMilliseconds(250)</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">};</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_timer.Tick += (o, arg) =&gt; ScanPreviewBuffer();</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>First we initialize a PhotoCamera instance. _previewVideo is the VideoBrush set in the rectangle, we set its source to the PhotoCamera instance. On line 8 we state that when the hardware camera button is half pressed we focus the camera, just a small helper for when the app has troubles reading the QR code. The next part is calling the PhotoChooserTask, this task gives us access to the albums and pictures on the device. We&rsquo;ll need a timer as well, every timer tick we&rsquo;ll check if the camera preview window contains a QR code. We&rsquo;ve declared some event handlers in this constructor, let&rsquo;s go over them one by one. We&rsquo;ll start with the OnPhotoCameraInitialized.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:25ca8d4e-82ea-4991-b8fe-e16670c98664" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> OnPhotoCameraInitialized(</span><span style="background: #ffffff; color: #0000ff;">object</span><span style="background: #ffffff; color: #000000;"> sender, </span><span style="background: #ffffff; color: #2b91af;">CameraOperationCompletedEventArgs</span><span style="background: #ffffff; color: #000000;"> e)</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">int</span><span style="background: #ffffff; color: #000000;"> width = </span><span style="background: #ffffff; color: #2b91af;">Convert</span><span style="background: #ffffff; color: #000000;">.ToInt32(_photoCamera.PreviewResolution.Width);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">int</span><span style="background: #ffffff; color: #000000;"> height = </span><span style="background: #ffffff; color: #2b91af;">Convert</span><span style="background: #ffffff; color: #000000;">.ToInt32(_photoCamera.PreviewResolution.Height);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_photoCamera.FlashMode = </span><span style="background: #ffffff; color: #2b91af;">FlashMode</span><span style="background: #ffffff; color: #000000;">.Off;</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_luminance = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">PhotoCameraLuminanceSource</span><span style="background: #ffffff; color: #000000;">(width, height);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_reader = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">QRCodeReader</span><span style="background: #ffffff; color: #000000;">();</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Dispatcher.BeginInvoke(() =&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_previewTransform.Rotation = _photoCamera.Orientation;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">});</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>We get the resolution&rsquo;s width and height and turn the flash off. The PhotoCameraLuminanceSource on line 7 is a custom class that will provide us with a previewbuffer that we can fill. Here&rsquo;s the class</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:6784ab28-9e1d-4a45-aa46-73489ca89ec4" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">class</span><span style="background: #ffffff; color: #2b91af;">PhotoCameraLuminanceSource</span><span style="background: #ffffff; color: #000000;"> : </span><span style="background: #ffffff; color: #2b91af;">LuminanceSource</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #2b91af;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">byte</span><span style="background: #ffffff; color: #000000;">[] PreviewBufferY { </span><span style="background: #ffffff; color: #0000ff;">get</span><span style="background: #ffffff; color: #000000;">; </span><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">set</span><span style="background: #ffffff; color: #000000;">; }</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #000000;"> PhotoCameraLuminanceSource(</span><span style="background: #ffffff; color: #0000ff;">int</span><span style="background: #ffffff; color: #000000;"> width, </span><span style="background: #ffffff; color: #0000ff;">int</span><span style="background: #ffffff; color: #000000;"> height)</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">: </span><span style="background: #ffffff; color: #0000ff;">base</span><span style="background: #ffffff; color: #000000;">(width, height)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">PreviewBufferY = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #0000ff;">byte</span><span style="background: #ffffff; color: #000000;">[width * height];</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">override</span><span style="background: #ffffff; color: #0000ff;">sbyte</span><span style="background: #ffffff; color: #000000;">[] Matrix</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">get</span><span style="background: #ffffff; color: #000000;"> { </span><span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #000000;"> (</span><span style="background: #ffffff; color: #0000ff;">sbyte</span><span style="background: #ffffff; color: #000000;">[])(</span><span style="background: #ffffff; color: #2b91af;">Array</span><span style="background: #ffffff; color: #000000;">)PreviewBufferY; }</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">override</span><span style="background: #ffffff; color: #0000ff;">sbyte</span><span style="background: #ffffff; color: #000000;">[] getRow(</span><span style="background: #ffffff; color: #0000ff;">int</span><span style="background: #ffffff; color: #000000;"> y, </span><span style="background: #ffffff; color: #0000ff;">sbyte</span><span style="background: #ffffff; color: #000000;">[] row)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (row == </span><span style="background: #ffffff; color: #0000ff;">null</span><span style="background: #ffffff; color: #000000;"> || row.Length &lt; Width)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">row = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #0000ff;">sbyte</span><span style="background: #ffffff; color: #000000;">[Width];</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">for</span><span style="background: #ffffff; color: #000000;"> (</span><span style="background: #ffffff; color: #0000ff;">int</span><span style="background: #ffffff; color: #000000;"> i = 0; i &lt; Height; i++)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">row[i] = (</span><span style="background: #ffffff; color: #0000ff;">sbyte</span><span style="background: #ffffff; color: #000000;">)PreviewBufferY[i * Width + y];</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #000000;"> row;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>The class inherits from LuminanceSource which comes from the Google zxing project. Zxing is a library to decode QR and barcode images, it has a .NET port on codeplex (<a title="http://zxingnet.codeplex.com/" href="http://zxingnet.codeplex.com/">http://zxingnet.codeplex.com/</a>) that port is what I use in this project and that&rsquo;s where the LuminanceSource comes from. That&rsquo;s also where the QRCodeReader class lives.</p>
<p>Next event handler that we attached in the constructor is the photoChooserTask_Completed</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:8c21e74c-4afd-4a91-bef4-a6bb3909ad15" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> photoChooserTask_Completed(</span><span style="background: #ffffff; color: #0000ff;">object</span><span style="background: #ffffff; color: #000000;"> sender, </span><span style="background: #ffffff; color: #2b91af;">PhotoResult</span><span style="background: #ffffff; color: #000000;"> e)</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (e.TaskResult == </span><span style="background: #ffffff; color: #2b91af;">TaskResult</span><span style="background: #ffffff; color: #000000;">.OK)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_imgStream = e.ChosenPhoto;</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_timer.Start();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>If the task returns succesfully we&rsquo;ll set the chosen photo, which arrives here as a stream, to the _imgStream field and we start the timer. Now on every timer tick (every 250 milliseconds in this example) we will scan the previewbuffer for QR codes.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:9c5cf68d-e4ec-49c2-973d-3664a24f200e" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> ScanPreviewBuffer()</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (_guid != </span><span style="background: #ffffff; color: #0000ff;">null</span><span style="background: #ffffff; color: #000000;">)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_timer.Stop();</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">SendImage();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">try</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_photoCamera.GetPreviewBufferY(_luminance.PreviewBufferY);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> binarizer = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">HybridBinarizer</span><span style="background: #ffffff; color: #000000;">(_luminance);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> binBitmap = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">BinaryBitmap</span><span style="background: #ffffff; color: #000000;">(binarizer);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> result = _reader.decode(binBitmap);</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Dispatcher.BeginInvoke(() =&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_guid = result.Text;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">});</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">catch</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>First thing we do here is checking if we already have a guid, if we do we stop the timer and send the image to the SignalR service. If _guid is still null we&rsquo;ll get the previewbuffer and try to decode it, if there&rsquo;s no QR code in the previewbuffer it will throw an exception, hence the empty catch block. When we can decode&nbsp; we&rsquo;ll go to the SendImage() method.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:4da61230-f7eb-469e-890b-61e2a87632f1" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">async</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> SendImage()</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (_connection == </span><span style="background: #ffffff; color: #0000ff;">null</span><span style="background: #ffffff; color: #000000;"> || _connection.State != </span><span style="background: #ffffff; color: #2b91af;">ConnectionState</span><span style="background: #ffffff; color: #000000;">.Connected)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">await</span><span style="background: #ffffff; color: #000000;"> SetupSignalRConnection();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (_connection.State == </span><span style="background: #ffffff; color: #2b91af;">ConnectionState</span><span style="background: #ffffff; color: #000000;">.Connected || _connection.State == </span><span style="background: #ffffff; color: #2b91af;">ConnectionState</span><span style="background: #ffffff; color: #000000;">.Reconnecting)</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">MemoryStream</span><span style="background: #ffffff; color: #000000;"> s = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">MemoryStream</span><span style="background: #ffffff; color: #000000;">();</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_imgStream.CopyTo(s);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_mainHub.Invoke(</span><span style="background: #ffffff; color: #a31515;">"ShareImage"</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #0000ff;">object</span><span style="background: #ffffff; color: #000000;">[]{s.ToArray(), _guid}); </span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">else</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">MessageBox</span><span style="background: #ffffff; color: #000000;">.Show(</span><span style="background: #ffffff; color: #a31515;">"not connected"</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>If there&rsquo;s no active connection, we&rsquo;ll call the SetupSignalRConnection() method. if there is and we are connected we copy the imagestream into a MemoryStream and we invoke the ShareImage() method on the SignalR server, passing in the memorystream, converted into a byte array, and the guid we got from the qr code.</p>
<p>Now for the connection to the server.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:5b5c50a6-7bda-48c8-a2cb-2b7eb76f9f83" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">async</span><span style="background: #ffffff; color: #2b91af;">Task</span><span style="background: #ffffff; color: #000000;"> SetupSignalRConnection()</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_connection = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">HubConnection</span><span style="background: #ffffff; color: #000000;">(</span><span style="background: #ffffff; color: #a31515;">"http://pbclone.azurewebsites.net/"</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_connection.StateChanged += ConnectionOnStateChanged;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_mainHub = _connection.CreateHubProxy(</span><span style="background: #ffffff; color: #a31515;">"imghub"</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">await</span><span style="background: #ffffff; color: #000000;"> _connection.Start();</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_mainHub.Invoke(</span><span style="background: #ffffff; color: #a31515;">"Create"</span><span style="background: #ffffff; color: #000000;">, _guid);</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>We instantiate a new HubConnection with the url to the service as parameter. We generate a proxy for the hub we want to use and we call the start() method on the connection. It will connect to the hub, get the javascript proxy and translate it into a .net proxy. We then invoke the Create() method and pass in the guid so that our Windows Phone client joins the same group as the web client. The ConnectionOnStateChanged event handler is only used to update the textblock on the page to show whether or not we&rsquo;re connected.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:19e896bd-ad9a-4ea8-84e7-055ddb1ff070" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> ConnectionOnStateChanged(</span><span style="background: #ffffff; color: #2b91af;">StateChange</span><span style="background: #ffffff; color: #000000;"> stateChange)</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">switch</span><span style="background: #ffffff; color: #000000;"> (stateChange.NewState)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">case</span><span style="background: #ffffff; color: #2b91af;">ConnectionState</span><span style="background: #ffffff; color: #000000;">.Connecting:</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">StatusText.Text = </span><span style="background: #ffffff; color: #a31515;">"Connecting..."</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">break</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">case</span><span style="background: #ffffff; color: #2b91af;">ConnectionState</span><span style="background: #ffffff; color: #000000;">.Connected:</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Dispatcher.BeginInvoke(() =&gt; StatusText.Text = </span><span style="background: #ffffff; color: #a31515;">"Connected"</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">break</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">case</span><span style="background: #ffffff; color: #2b91af;">ConnectionState</span><span style="background: #ffffff; color: #000000;">.Reconnecting:</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Dispatcher.BeginInvoke(() =&gt; StatusText.Text = </span><span style="background: #ffffff; color: #a31515;">"Reconnecting..."</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">break</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">case</span><span style="background: #ffffff; color: #2b91af;">ConnectionState</span><span style="background: #ffffff; color: #000000;">.Disconnected:</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Dispatcher.BeginInvoke(() =&gt; StatusText.Text = </span><span style="background: #ffffff; color: #a31515;">"Disconnected"</span><span style="background: #ffffff; color: #000000;">);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">break</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>And that&rsquo;s it, we can now start sending images to any device with a browser and an internet connection.</p>
<h2>Conclusion</h2>
<p>In this post I&rsquo;ve tried to demystify the magic from Nokia&rsquo;s Photobeamer app. It is a really cool app but when you take it apart, it&rsquo;s not that magical. Like a lot of impressive looking tech it just combines a bunch of existing techs into something no one else thought of.</p>
<p>The code in this article is all my own, I have no clue how the Nokia app actually works and what technology they are using, I&rsquo;m only mimicking their behavior.</p>
<p>Update: I've uploaded the project to Github <a href="https://github.com/NicoVermeir/photobeamerclone">https://github.com/NicoVermeir/photobeamerclone</a></p>
{% include imported_disclaimer.html %}
