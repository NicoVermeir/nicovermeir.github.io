---
layout: post
title: "iis express and the windows phone 8 emulator"
date: 2013-05-31 20:35:00 +0200
comments: true
published: true
categories: ["post"]
tags: [".Net", "Devices", "IIS", "Metro", "Web development", "WP8"]
alias: ["/blog/post/2013/05/31/IIS-Express-and-the-Windows-Phone-8-emulator.aspx", "/blog/post/2013/05/31/iis-express-and-the-windows-phone-8-emulator.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2013/05/31/IIS-Express-and-the-Windows-Phone-8-emulator.aspx
 - /blog/post/2013/05/31/iis-express-and-the-windows-phone-8-emulator.aspx
---
<p>As an app developer you&rsquo;re bound to run into a situation where you&rsquo;re building both an app and a mobile website or REST based service. That means that you&rsquo;re testing the ASP.net project in IIS Express while using the Windows Phone 8 emulator, since the emulator behaves like a separate device on the network you can&rsquo;t use localhost to contact your IIS Express server.</p>
<p>There are a few ways to tackle this problem, you can finish the web project first and deploy it to a webserver. If your WP8 emulator is configured correctly it should have internet access and will be able to connect to your server just fine. A second option is to install an IIS server in your network and deploy to there from Visual Studio. But the most easy option would be to use the IIS Express server that comes with Visual Studio. That&rsquo;s certainly an option but requires some (small) configuration tweaks.</p>
<h2>First things first</h2>
<p>We&rsquo;ll need a webproject of course to test this. I&rsquo;ll create a very very basic ASP.net WebAPI project and a very basic Windows Phone 8 app that will run in the emulator. I could have made my point with a simple hello world website and the mobile browser but that&rsquo;s just boring.</p>
<p>Visual Studio 2012 ships with ASP.net MVC WebAPI, think REST services made ridiculously easy, to start a project select the MVC4 web template in Visual Studio 2012 and give it a name.</p>
<p><a href="http://i44.tinypic.com/6jd1fs.jpg" target="_blank"><img src="http://i44.tinypic.com/6jd1fs.jpg" alt="" width="435" height="301" /></a>&nbsp;</p>
<p>Once you click OK a second dialog will show up and that one holds the option to start a WebAPI project.</p>
<p><a href="http://i42.tinypic.com/5vw5rl.jpg" target="_blank"><img src="http://i42.tinypic.com/5vw5rl.jpg" alt="" width="435" height="394" /></a></p>
<p>What this gives you is an MVC project with an API controller. This behaves much like the normal controller that you&rsquo;re used to from MVC but instead of returning a view it returns data in a JSON format (serializing happens with JSON.net by the way, not with the .net serializer). I&rsquo;m not going to dive very deep in WebAPI, there are a lot of bloggers out there that know way more about this stuff than I do. We&rsquo;ll be using the default GET method from the ValuesController.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:33d22f4f-f4fe-4012-85b9-43107269095c" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #008000;">// GET api/values</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #2b91af;">IEnumerable</span><span style="background: #ffffff; color: #000000;">&lt;</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">&gt; Get()</span></li>
<li><span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">[] { </span><span style="background: #ffffff; color: #a31515;">"value1"</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #a31515;">"value2"</span><span style="background: #ffffff; color: #000000;"> };</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>This just returns a collection of two strings.</p>
<p>Next step is adding a Windows Phone 8 project to the solution (a simple basic project started from the normal template). Now we have two projects in one solution that both need to start up. We could launch the api without debugging and launch the WP8 app in debug mode or we can set them both to launch at debug by selecting multiple startup projects in the solution properties.</p>
<p><a href="http://i43.tinypic.com/opzg5s.jpg" target="_blank"><img src="http://i43.tinypic.com/opzg5s.jpg" alt="" width="429" height="270" /></a></p>
<p>You can easily see that the settings have applied successfully if no project in the solution is bold anymore.</p>
<p>Okay, time to hook them up. We&rsquo;ll need two things, the IP address of the pc running IIS Express and the port that the ASP.net project will use. Getting the IP address should be easy, just enter ipconfig in a command prompt. To find out the port, navigate to the properties of the ASP.net project.</p>
<p><a href="http://i41.tinypic.com/353ee8j.jpg" target="_blank"><img src="http://i41.tinypic.com/353ee8j.jpg" alt="" width="496" height="269" /></a></p>
<p>In the Web tab of the properties you can see the project url for the website, that url contains the port that will be used.</p>
<p>Time for some coding, in the Windows Phone app, add a constant that holds the url to the website.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:59305ae1-2943-482e-ac0f-f876afb3a774" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">const</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> Url = </span><span style="background: #ffffff; color: #a31515;">"http://192.168.0.116:7145/"</span><span style="background: #ffffff; color: #000000;">;</span></li>
</ol></div>
</div>
</div>
<p>And the code to fetch the REST data</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:948dc13a-d6ea-4ab5-831f-78ba65bcaf1c" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> FetchData()</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">WebClient</span><span style="background: #ffffff; color: #000000;"> client = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">WebClient</span><span style="background: #ffffff; color: #000000;">();</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">client.DownloadStringCompleted += ClientOnDownloadStringCompleted;</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">client.DownloadStringAsync(</span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">Uri</span><span style="background: #ffffff; color: #000000;">(Url + </span><span style="background: #ffffff; color: #a31515;">"api/values"</span><span style="background: #ffffff; color: #000000;">));</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>The callback will use Json.net (added through NuGet) to deserialize the values into a List&lt;string&gt; that is then set as the ItemsSource of a LongListSelector.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:c292825c-521c-4e94-9e77-a7657cc19ef8" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> ClientOnDownloadStringCompleted(</span><span style="background: #ffffff; color: #0000ff;">object</span><span style="background: #ffffff; color: #000000;"> sender, </span><span style="background: #ffffff; color: #2b91af;">DownloadStringCompletedEventArgs</span><span style="background: #ffffff; color: #000000;"> downloadStringCompletedEventArgs)</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (downloadStringCompletedEventArgs.Error == </span><span style="background: #ffffff; color: #0000ff;">null</span><span style="background: #ffffff; color: #000000;">)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> values = </span><span style="background: #ffffff; color: #2b91af;">JsonConvert</span><span style="background: #ffffff; color: #000000;">.DeserializeObject&lt;</span><span style="background: #ffffff; color: #2b91af;">List</span><span style="background: #ffffff; color: #000000;">&lt;</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">&gt;&gt;(downloadStringCompletedEventArgs.Result);</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">ValueList.ItemsSource = values;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>All in all pretty easy but this won&rsquo;t work because IIS Express is bound to localhost only by default, we&rsquo;ll need to change the config to allow external connections.</p>
<p>The configuration of IIS Express can be found in the applicationhost.config xml file found in %userprofile%\documents\IISExpress\config (just copy-paste this path into the Windows 8 start screen and press enter)</p>
<p>Open the XML file and search for the name of your ASP.net project. It should look something like this</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:58e8acbf-ad7d-45e3-8611-3a8c32c0a481" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #000000;">&lt;site name=</span><span style="background: #ffffff; color: #a31515;">"IisExpressDemo"</span><span style="background: #ffffff; color: #000000;"> id=</span><span style="background: #ffffff; color: #a31515;">"13"</span><span style="background: #ffffff; color: #000000;">&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;application path=</span><span style="background: #ffffff; color: #a31515;">"/"</span><span style="background: #ffffff; color: #000000;"> applicationPool=</span><span style="background: #ffffff; color: #a31515;">"Clr4IntegratedAppPool"</span><span style="background: #ffffff; color: #000000;">&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;virtualDirectory path=</span><span style="background: #ffffff; color: #a31515;">"/"</span><span style="background: #ffffff; color: #000000;"> physicalPath=</span><span style="background: #ffffff; color: #a31515;">"c:\users\nico\documents\visual studio 2012\Projects\IisExpressDemo\IisExpressDemo"</span><span style="background: #ffffff; color: #000000;"> /&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;/application&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;bindings&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;binding protocol=</span><span style="background: #ffffff; color: #a31515;">"http"</span><span style="background: #ffffff; color: #000000;"> bindingInformation=</span><span style="background: #ffffff; color: #a31515;">"*:7145:localhost"</span><span style="background: #ffffff; color: #000000;"> /&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;/bindings&gt;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">&lt;/site&gt;</span></li>
</ol></div>
</div>
</div>
<p>Line 6 contains the binding, copy this line and paste it underneath, change localhost with the IP address of your pc, like this</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:0530aa62-80ce-41e1-86c3-7fc0715d108f" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #000000;">&lt;site name=</span><span style="background: #ffffff; color: #a31515;">"IisExpressDemo"</span><span style="background: #ffffff; color: #000000;"> id=</span><span style="background: #ffffff; color: #a31515;">"13"</span><span style="background: #ffffff; color: #000000;">&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;application path=</span><span style="background: #ffffff; color: #a31515;">"/"</span><span style="background: #ffffff; color: #000000;"> applicationPool=</span><span style="background: #ffffff; color: #a31515;">"Clr4IntegratedAppPool"</span><span style="background: #ffffff; color: #000000;">&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;virtualDirectory path=</span><span style="background: #ffffff; color: #a31515;">"/"</span><span style="background: #ffffff; color: #000000;"> physicalPath=</span><span style="background: #ffffff; color: #a31515;">"c:\users\nico\documents\visual studio 2012\Projects\IisExpressDemo\IisExpressDemo"</span><span style="background: #ffffff; color: #000000;"> /&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;/application&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;bindings&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;binding protocol=</span><span style="background: #ffffff; color: #a31515;">"http"</span><span style="background: #ffffff; color: #000000;"> bindingInformation=</span><span style="background: #ffffff; color: #a31515;">"*:7145:localhost"</span><span style="background: #ffffff; color: #000000;"> /&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;binding protocol=</span><span style="background: #ffffff; color: #a31515;">"http"</span><span style="background: #ffffff; color: #000000;"> bindingInformation=</span><span style="background: #ffffff; color: #a31515;">"*:7145:192.168.0.116"</span><span style="background: #ffffff; color: #000000;"> /&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">&lt;/bindings&gt;</span></li>
<li><span style="background: #ffffff; color: #000000;">&lt;/site&gt;</span></li>
</ol></div>
</div>
</div>
<p>Close the IIS Express server if it&rsquo;s still running and run the project.</p>
<p>Now there&rsquo;s a pretty big chance that you&rsquo;ll see this.</p>
<p><img src="http://i44.tinypic.com/ekq8hh.jpg" alt="" /></p>
<p>Visual Studio 2012 needs to be started as administrator to allow IIS Express to create bindings for external connections, so if you get this error, close and restart Visual Studio 2012 as an administrator.</p>
<p>Run the project again and the Windows Phone application should be able to fetch the data from the API.</p>
<p><img src="http://i43.tinypic.com/ab5zli.jpg" alt="" /></p>
<p>&nbsp;</p>
<h2>Conclusion</h2>
<p>In this post I&rsquo;ve talked about opening up your IIS Express development server to allow external connections. This is needed to allow the Windows Phone 8 emulator to connect to websites or APIs hosted locally. It&rsquo;s basically two steps</p>
<ul>
<li>Add the IP address of your pc to the IIS config</li>
<li>Launch Visual Studio as administrator to be able to set the binding</li>
</ul>
<p>&nbsp;</p>
<p>
<script type="text/javascript">// <![CDATA[
google_ad_client = "ca-pub-2343948435149147";
/* spikie.be underpost */
google_ad_slot = "9230774310";
google_ad_width = 468;
google_ad_height = 60;
// ]]
{% include imported_disclaimer.html %}
