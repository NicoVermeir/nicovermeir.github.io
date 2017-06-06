---
layout: post
title: "10 things you might have missed about mvvm light"
date: 2013-04-12 13:22:00 +0200
comments: true
published: true
categories: ["post"]
tags: [".Net", "MVVM Light", "Windows 8", "WinRT", "WP7", "WP8", "XAML"]
alias: ["/blog/post/2013/04/12/10-things-you-might-have-missed-about-MVVM-Light.aspx", "/blog/post/2013/04/12/10-things-you-might-have-missed-about-mvvm-light.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2013/04/12/10-things-you-might-have-missed-about-MVVM-Light.aspx
 - /blog/post/2013/04/12/10-things-you-might-have-missed-about-mvvm-light.aspx
---
<p>Ever since I started playing with XAML based technologies (which actually isn&rsquo;t that long ago) I&rsquo;ve been looking into the MVVM (Model &ndash; View &ndash; ViewModel) pattern. I stumbled across MVVM Light pretty soon and liked the way it worked. Turns out I&rsquo;m not the only one that likes it, there&rsquo;s a whole set of developers, both hobby and professional, that really love this set of libraries. MVVM Light is, according to the author, not a framework but a set of libraries that take care of the plumbing to set up an MVVM structure and provide some extra helper classes to make life easier.</p>
<p>MVVM Light has changed a lot in its history, some elements were dragged out, others dragged in. Fact remains that it&rsquo;s a fast, easy to use and lightweight framework. The author, Laurent Bugnion, does a great job of listening to the people that use MVVM Light, incorporating requested features and helping developers out. While talking to some of my fellow developers I&rsquo;ve noticed a few times that there are certain elements of MVVM Light that others hadn&rsquo;t heard of, and the same goes in the other direction. I&rsquo;ve learned a lot of new things about MVVM Light just from talking with other users. Thinking about that gave me the idea of this blogpost and since those &ldquo;10 things about&hellip;&rdquo; posts seem to be popular, this was my chance. So here are my top 10 hidden gems of MVVM Light that you might have missed.</p>
<h2>1. The MVVM Light installer</h2>
<p>This one might seem a bit obvious, but in this NuGet driven world we would forget the added benefit of an installer. MVVM Light has an MSI installer that not only installs the binaries on your drive but it also provides project and itemtemplates in Visual Studio, along with a list of snippets. In case the Visual Studio 2012 update 2 removed your templates, reinstall the VSIX from C:\Program Files (x86)\Laurent Bugnion (GalaSoft)\Mvvm Light Toolkit\Vsix that should put the project templates back in place.</p>
<p><a href="http://i48.tinypic.com/15wbh94.jpg" target="_blank"><img src="http://i48.tinypic.com/15wbh94.jpg" alt="" width="409" height="283" /></a></p>
<h2>2. Constructor injection</h2>
<p>This one is just awesome, and is actually a feature that can be found in most DI frameworks. MVVM Light uses SimpleIoc to register viewmodels and service classes at application launch (or during the app lifetime). Constructor injection means that you can specify a parameter in a class his constructor. When that class gets instantiated SimpleIoc will try to find a registered class of the same type as the parameter, when it finds one, that instance will get injected as the parameter of the constructor. Here&rsquo;s an example, let&rsquo;s say that in the ViewModelLocator, we register a navigation service.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:18154e96-5e91-47fd-9de3-0c8e97666d37" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #2b91af;">SimpleIoc</span><span style="background: #ffffff; color: #000000;">.Default.Register&lt;</span><span style="background: #ffffff; color: #2b91af;">INavigationService</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #2b91af;">NavigationService</span><span style="background: #ffffff; color: #000000;">&gt;();</span></li>
</ol></div>
</div>
</div>
<p>We state here that we want to register an INavigationService in the IOC container, when it creates the instance we want it to be of type NavigationService. This &ldquo;record&rdquo; in the IOC container doesn&rsquo;t have an instance yet, it gets instantiated when we fetch it from the container the first time. There are some occasions where you would want to create an instance of a class immediately when it gets registered. the Register&lt;T&gt; function of SimpleIoc has an overload to do just that.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:b99ec395-3036-4e5c-9f21-7a42b0d1d52e" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #2b91af;">SimpleIoc</span><span style="background: #ffffff; color: #000000;">.Default.Register&lt;</span><span style="background: #ffffff; color: #2b91af;">INavigationService</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #2b91af;">NavigationService</span><span style="background: #ffffff; color: #000000;">&gt;(</span><span style="background: #ffffff; color: #0000ff;">true</span><span style="background: #ffffff; color: #000000;">);</span></li>
</ol></div>
</div>
</div>
<p>Just pass in true as a parameter and it will create an instance right there and then.</p>
<p>Now we want to use the NavigationService in the MainViewModel.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:419ab7a4-f687-48ea-908d-a1559553d3a8" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #808080;">///</span><span style="background: #ffffff; color: #808080;">&lt;summary&gt;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #808080;">///</span><span style="background: #ffffff; color: #008000;"> Initializes a new instance of the MainViewModel class.</span></li>
<li><span style="background: #ffffff; color: #808080;">///</span><span style="background: #ffffff; color: #808080;">&lt;/summary&gt;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #000000;"> MainViewModel(</span><span style="background: #ffffff; color: #2b91af;">INavigationService</span><span style="background: #ffffff; color: #000000;"> navigationService)</span></li>
<li><span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;</li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>SimpleIoc will search for a registered class of type INavigationService and will inject it in this constructor. This saves us the hassle of manually contacting the IOC container and requesting the correct instance.</p>
<p>WARNING: do be careful with this, the order in which you register your classes with the IOC container can be important, especially when using the overload to create instances. If I would create the MainViewModel before the NavigationService is registered I would get a nullreference exception. So be aware of that.</p>
<h2>3. SimpleIoc to simple? replace it!</h2>
<p>The SimpleIoc library works great and is a cool, lightweight addition to MVVM Light, but it is actually really lightweight. It is a very realistic scenario that for larger apps the SimpleIoc just won&rsquo;t do (or you&rsquo;re like me and want to try out how hard it is to replace it with another one). In this example I&rsquo;m going to replace SimpleIoc with AutoFac, another well known and very powerful IOC service.</p>
<p>First of all, we&rsquo;re going to need the AutoFac libraries and the extra library that allows us to use the ServiceLocator, just like SimpleIoc does. So either from the package manager console or from the UI, add the CommonServiceLocator extra for AutoFac, the AutoFac libraries are a dependency so they&rsquo;ll get installed as well. I&rsquo;m using a brand new Windows Phone 8 project for this, started from the MVVM Light project template.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:faeef52f-718c-42a8-ab5d-2b97ea97b9f5" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li>Install-Package Autofac.Extras.CommonServiceLocator</li>
</ol></div>
</div>
</div>
<p>The only place we&rsquo;ll need to change some code is in the ViewModelLocator.</p>
<p>This is the new ViewModelLocator constructor, I&rsquo;ve put the old SimpleIoc code in comments so it&rsquo;s easy to compare</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:442d38a5-67da-4977-b6a2-a278e426cb88" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">static</span><span style="background: #ffffff; color: #000000;"> ViewModelLocator()</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> container = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">ContainerBuilder</span><span style="background: #ffffff; color: #000000;">();</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//ServiceLocator.SetLocatorProvider(() =&gt; SimpleIoc.Default);</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">ServiceLocator</span><span style="background: #ffffff; color: #000000;">.SetLocatorProvider(() =&gt; </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">AutofacServiceLocator</span><span style="background: #ffffff; color: #000000;">(container.Build()));</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (</span><span style="background: #ffffff; color: #2b91af;">ViewModelBase</span><span style="background: #ffffff; color: #000000;">.IsInDesignModeStatic)</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//SimpleIoc.Default.Register&lt;IDataService, Design.DesignDataService&gt;();</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">container.RegisterType&lt;Design.</span><span style="background: #ffffff; color: #2b91af;">DesignDataService</span><span style="background: #ffffff; color: #000000;">&gt;().As&lt;</span><span style="background: #ffffff; color: #2b91af;">IDataService</span><span style="background: #ffffff; color: #000000;">&gt;();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">else</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//SimpleIoc.Default.Register&lt;IDataService, DataService&gt;();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">container.RegisterType&lt;</span><span style="background: #ffffff; color: #2b91af;">DataService</span><span style="background: #ffffff; color: #000000;">&gt;().As&lt;</span><span style="background: #ffffff; color: #2b91af;">IDataService</span><span style="background: #ffffff; color: #000000;">&gt;();</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//SimpleIoc.Default.Register&lt;MainViewModel&gt;();</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">container.RegisterType&lt;</span><span style="background: #ffffff; color: #2b91af;">MainViewModel</span><span style="background: #ffffff; color: #000000;">&gt;();</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>And that&rsquo;s it, we declare a ContainerBuilder, set it as the LocatorProvider. The container is then used to register everything we need. The SimpleIoc overload that creates an instance upon registering would look something like this in AutoFac.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:f5ccea80-41b3-4b52-8ee5-ae44e98533ee" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #000000;">container.RegisterInstance(</span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">DataService</span><span style="background: #ffffff; color: #000000;">()).As&lt;</span><span style="background: #ffffff; color: #2b91af;">IDataService</span><span style="background: #ffffff; color: #000000;">&gt;();</span></li>
</ol></div>
</div>
</div>
<p>That&rsquo;s it, constructor injection should still work exactly like before with SimpleIoc.</p>
<h2>4. Built-in messages</h2>
<p>MVVM Light has something called the messenger, it registers classes as listeners and can send messages to them. This is commonly used to do communication between viewmodels. Generally I would create a message class for each type of message that I want to send, but MVVM Light has some build in messages that we can use.</p>
<p><strong>GenericMessage&lt;T&gt;(T content)</strong> A message that can contain whatever of type T.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:06b7c11a-e552-479b-9139-1c28fd1c30be" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #2b91af;">Messenger</span><span style="background: #ffffff; color: #000000;">.Default.Send(</span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">GenericMessage</span><span style="background: #ffffff; color: #000000;">&lt;</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">&gt;(</span><span style="background: #ffffff; color: #a31515;">"my message"</span><span style="background: #ffffff; color: #000000;">));</span></li>
</ol></div>
</div>
</div>
<pre><strong>NotificationMessage(string notification)</strong>a message that contains a notification. this might be<br />used to send a notification to a notification factory that will show the message in the preferred way.</pre>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:f034dfd1-2c0e-4e00-88f7-a49cf45e6e34" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">try</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//try something</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #008000;">}</span></li>
<li><span style="background: #ffffff; color: #0000ff;">catch</span><span style="background: #ffffff; color: #000000;"> (</span><span style="background: #ffffff; color: #2b91af;">Exception</span><span style="background: #ffffff; color: #000000;"> ex)</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">Messenger</span><span style="background: #ffffff; color: #000000;">.Default.Send(</span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">NotificationMessage</span><span style="background: #ffffff; color: #000000;">(ex.Message));</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>There&rsquo;s also a <strong>NotificationMessage&lt;T&gt;(T notification)</strong> should you need it.</p>
<p>The next one is NotificationMessageAction(string notification, Action callback) basically the same as the NotificationMessage but you can add a callback action that will fire once the message is received. This one also has the generic implementation just like NotificationMessage.</p>
<pre><strong>DialogMessage(string content, Action&lt;MessageBoxResult&gt; callback) <br /></strong>This message is meant to ask the user to input something and it will return the result of that input in the <br />MessageBoxResult. MessageBoxResult is an enum that lives in System.Windows</pre>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:5bf3dc72-80ae-46c4-a66f-dd1d7a2ffb14" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">enum</span><span style="background: #ffffff; color: #2b91af;">MessageBoxResult</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #2b91af;">{</span></li>
<li>&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">None = 0,</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">OK = 1,</span></li>
<li>&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Cancel = 2,</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">Yes = 6,</span></li>
<li>&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">No = 7,</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>&nbsp;</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:bfb9b26c-2087-4d93-b16e-501f5be15d72" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #2b91af;">Messenger</span><span style="background: #ffffff; color: #000000;">.Default.Send(</span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">DialogMessage</span><span style="background: #ffffff; color: #000000;">(</span><span style="background: #ffffff; color: #a31515;">"Are you sure?"</span><span style="background: #ffffff; color: #000000;">, result =&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (result == </span><span style="background: #ffffff; color: #2b91af;">MessageBoxResult</span><span style="background: #ffffff; color: #000000;">.Yes)</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//do something</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}));</span></li>
</ol></div>
</div>
</div>
<p>The DialogMessage class inherits from GenericMessage&lt;string&gt;</p>
<pre><strong>PropertyChangedMessage(T oldValue, T newValue, string propertyName)</strong>
The PropertyChangedMessage is meant to use like the RaisePropertyChanged implementation. This is great when multiple <br />viewmodels need to respond to a changed property.</pre>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:e5fc6761-b01d-4d43-8506-39ff8b85538d" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2.5em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> WelcomeTitle</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">get</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #000000;"> _welcomeTitle;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li>&nbsp;</li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">set</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">if</span><span style="background: #ffffff; color: #000000;"> (_welcomeTitle == </span><span style="background: #ffffff; color: #0000ff;">value</span><span style="background: #ffffff; color: #000000;">)</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #2b91af;">Messenger</span><span style="background: #ffffff; color: #000000;">.Default.Send(</span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">PropertyChangedMessage</span><span style="background: #ffffff; color: #000000;">&lt;</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">&gt;(_welcomeTitle, </span><span style="background: #ffffff; color: #0000ff;">value</span><span style="background: #ffffff; color: #000000;">, </span><span style="background: #ffffff; color: #a31515;">"WelcomeTitle"</span><span style="background: #ffffff; color: #000000;">));</span></li>
<li style="background: #f3f3f3;">&nbsp;</li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">_welcomeTitle = </span><span style="background: #ffffff; color: #0000ff;">value</span><span style="background: #ffffff; color: #000000;">;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">RaisePropertyChanged(WelcomeTitlePropertyName);</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">}</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>Be careful when registering listeners, try to use as many different types of messages as makes sense. You don&rsquo;t want a wrong listener to receive a message because it happens to listen to the same type of message. To register a listener do this:</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:35fe87f6-5074-4e70-abb8-6fea55f48600" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #2b91af;">Messenger</span><span style="background: #ffffff; color: #000000;">.Default.Register&lt;</span><span style="background: #ffffff; color: #2b91af;">PropertyChangedMessage</span><span style="background: #ffffff; color: #000000;">&lt;</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">&gt;&gt;(</span><span style="background: #ffffff; color: #0000ff;">this</span><span style="background: #ffffff; color: #000000;">, message =&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">var</span><span style="background: #ffffff; color: #000000;"> a = message.NewValue;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//do something</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">} );</span></li>
</ol></div>
</div>
</div>
<h2>5. Portable libraries</h2>
<p>MVVM Light is available on every XAML based platform. And it comes with a portable version now. The portable version is a separate library on NuGet.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:4b35931c-7128-457a-8475-4f7fedf7eb9b" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li>Install-Package Portable.MvvmLightLibs</li>
</ol></div>
</div>
</div>
<p>If you decide to use the portable version, make sure that every project in your solution that needs the MVVM Light libraries references the portable version. It does not work together with the &ldquo;normal&rdquo; MVVM Light libraries. When you use the PCL version, you can put your viewmodels in a separate, portable library and share them over, for example, a Windows Store and a Windows Phone app.</p>
<h2>6. Event to Command behavior</h2>
<p>MVVM Light has an ICommand implementation called RelayCommand that can be used to bind commands to actions. Like for example a button in XAML has a Command property that can be bound to an ICommand on its datacontext, so that when the button is clicked the ICommand will fire. Unfortunately not every XAML UI element has a bindable command property for every event that they can trigger and that&rsquo;s where EventToCommand comes into play. With EventToCommand you can bind any event from a XAML UI element to an ICommand in the viewmodel.</p>
<p>First we&rsquo;ll need two namespaces in our XAML page</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:7df46613-d113-4e4a-a5b8-fa8a46fad4c4" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div class="le-pavsc-container">
<div class="le-pavsc-titleblock">Code Snippet</div>
<div style="background: #ddd; max-height: 500px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #ff0000;">xmlns</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #ff0000;">i</span><span style="background: #ffffff; color: #0000ff;">="clr-namespace:System.Windows.Interactivity;assembly=System.Windows.Interactivity"</span></li>
<li class="le-pavsc-even"><span style="background: #ffffff; color: #ff0000;">xmlns</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #ff0000;">command</span><span style="background: #ffffff; color: #0000ff;">="clr-namespace:GalaSoft.MvvmLight.Command;assembly=GalaSoft.MvvmLight.Extras.WP8"</span></li>
</ol></div>
</div>
</div>
<p>Let&rsquo;s say that we want to use the Tap event on a stackpanel.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:3073c2c6-de85-4298-8758-965e96bc9cc6" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">StackPanel</span><span style="background: #ffffff; color: #ff0000;"> Grid.Row</span><span style="background: #ffffff; color: #0000ff;">="0"</span><span style="background: #ffffff; color: #ff0000;"> Orientation</span><span style="background: #ffffff; color: #0000ff;">="Horizontal"&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">i</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #a31515;">Interaction.Triggers</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">i</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #a31515;">EventTrigger</span><span style="background: #ffffff; color: #ff0000;"> EventName</span><span style="background: #ffffff; color: #0000ff;">="Tap"&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">command</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #a31515;">EventToCommand</span><span style="background: #ffffff; color: #ff0000;"> Command</span><span style="background: #ffffff; color: #0000ff;">="{</span><span style="background: #ffffff; color: #a31515;">Binding</span><span style="background: #ffffff; color: #ff0000;"> GoToCommand}</span><span style="background: #ffffff; color: #0000ff;">"</span><span style="background: #ffffff; color: #ff0000;"> CommandParameter</span><span style="background: #ffffff; color: #0000ff;">="Edit" /&gt;</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #a31515;">i</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #a31515;">EventTrigger</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">&lt;/</span><span style="background: #ffffff; color: #a31515;">i</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #a31515;">Interaction.Triggers</span><span style="background: #ffffff; color: #0000ff;">&gt;</span></li>
</ol></div>
</div>
</div>
<p>Line 3 specifies the event that we want to handle, note that this is a string so be aware of typos. Line 4 binds the actual command and can even pass a parameter to the ICommand implementation.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:88867710-e602-4e9f-9dd6-5149c9629845" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #2b91af;">RelayCommand</span><span style="background: #ffffff; color: #000000;">&lt;</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">&gt; _goToCommand;</span></li>
<li style="background: #f3f3f3;"><span style="background: #ffffff; color: #0000ff;">public</span><span style="background: #ffffff; color: #2b91af;">RelayCommand</span><span style="background: #ffffff; color: #000000;">&lt;</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">&gt; GoToCommand</span></li>
<li><span style="background: #ffffff; color: #000000;">{</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #0000ff;">get</span><span style="background: #ffffff; color: #000000;"> { </span><span style="background: #ffffff; color: #0000ff;">return</span><span style="background: #ffffff; color: #000000;"> _goToCommand jQuery15206875578026641675_1366095632942 (_goToCommand = </span><span style="background: #ffffff; color: #0000ff;">new</span><span style="background: #ffffff; color: #2b91af;">RelayCommand</span><span style="background: #ffffff; color: #000000;">&lt;</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;">&gt;(NavigateAway)); }</span></li>
<li><span style="background: #ffffff; color: #000000;">}</span></li>
</ol></div>
</div>
</div>
<p>The NavigateAway method has this signature</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:3d610a1c-5722-4087-bdc9-b93c8fa95962" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">private</span><span style="background: #ffffff; color: #0000ff;">void</span><span style="background: #ffffff; color: #000000;"> NavigateAway(</span><span style="background: #ffffff; color: #0000ff;">string</span><span style="background: #ffffff; color: #000000;"> parameter)</span></li>
</ol></div>
</div>
</div>
<p>The parameter will be the word &ldquo;Edit&rdquo; in this case as that&rsquo;s what we&rsquo;ve specified in the XAML. We can even pass the eventargs from the event to the Command by changing line 4 from the XAML snippet to this</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:74f4dc59-1e2c-4f5f-981d-fe911d92b507" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #0000ff;">&lt;</span><span style="background: #ffffff; color: #a31515;">command</span><span style="background: #ffffff; color: #0000ff;">:</span><span style="background: #ffffff; color: #a31515;">EventToCommand</span><span style="background: #ffffff; color: #ff0000;"> PassEventArgsToCommand</span><span style="background: #ffffff; color: #0000ff;">="True"</span><span style="background: #ffffff; color: #ff0000;"> Command</span><span style="background: #ffffff; color: #0000ff;">="{</span><span style="background: #ffffff; color: #a31515;">Binding</span><span style="background: #ffffff; color: #ff0000;"> GoToCommand}</span><span style="background: #ffffff; color: #0000ff;">"</span><span style="background: #ffffff; color: #0000ff;"> /&gt;</span></li>
</ol></div>
</div>
</div>
<p>Windows Store applications don&rsquo;t have these behaviors out of the box so you won&rsquo;t be able to use EventToCommand there unless you install the Win8nl toolkit from NuGet. Joost Van Schaik has build his own implementation of behaviors in WinRT and thanks to his efforts (and of some other people that have helped in the project) we can now use EventToCommand in WinRT.</p>
<h2>7. DispatcherHelper</h2>
<p>Since .net 4.5 we have the await/async keywords and being the good little citizens that we are we do a lot of stuff async now. That means if we want to update something that lives on the UI thread we&rsquo;ll need the Dispatcher class to marshall our action to that thread. Normally we don&rsquo;t have access to the Dispatcher from our viewmodel classes. MVVM Light contains a DispatcherHelper that will execute an action on the UI thread when needed.</p>
<div id="scid:9ce6104f-a9aa-4a17-a79f-3a39532ebf7c:870ba6fd-080b-461e-99ea-c55b9b13f9de" class="wlWriterEditableSmartContent" style="float: none; margin: 0px; display: inline; padding: 0px;">
<div style="border: #000080 1px solid; color: #000; font-family: 'Courier New', Courier, Monospace; font-size: 10pt;">
<div style="background: #000080; color: #fff; font-family: Verdana, Tahoma, Arial, sans-serif; font-weight: bold; padding: 2px 5px;">Code Snippet</div>
<div style="background: #ddd; max-height: 300px; overflow: auto;"><ol style="background: #ffffff; margin: 0 0 0 2em; padding: 0 0 0 5px;" start="1">
<li><span style="background: #ffffff; color: #2b91af;">DispatcherHelper</span><span style="background: #ffffff; color: #000000;">.CheckBeginInvokeOnUI(() =&gt;</span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">{</span></li>
<li>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #008000;">//do something </span></li>
<li style="background: #f3f3f3;">&nbsp;&nbsp;&nbsp;&nbsp;<span style="background: #ffffff; color: #000000;">});</span></li>
</ol></div>
</div>
</div>
<p>The DispatcherHelper gets initialized in the App.xaml.cs in the InitializePhoneApplication method (in a WP8 project that is).</p>
<p>DispatcherHelper also has a RunAsync method. The difference with the CheckBeginInvokeOnUI is that the CheckBeginInvokeOnUI will first check if it&rsquo;s already on the UI thread, if it is it will just execute the action, if it isn&rsquo;t it will call the RunAsync method.</p>
<h2>8. Blendable</h2>
<p>MVVM Light has complete Blend support, meaning you can drag and drop properties from the viewmodel onto the view to generate a binding, or you can generate design time data based on the datacontext and so on. I&rsquo;m really not that good in Blend so I&rsquo;m not going into detail about this one, just remember that MVVM Light was build with Blend in mind.</p>
<h2>9. Open Source</h2>
<p>This one you probably knew but MVVM Light is completely open source. <a title="http://mvvmlight.codeplex.com/" href="http://mvvmlight.codeplex.com/">http://mvvmlight.codeplex.com/</a> is the place to be if you want to dive into the source code.</p>
<h2>10. Laurent is on Twitter and he&rsquo;s a nice guy <img class="wlEmoticon wlEmoticon-smile" src="http://www.spikie.be/blog/images/wlEmoticon-smile_18.png" alt="Glimlach" /></h2>
<p>Laurent Bugnion, the founder of MVVM Light, is on Twitter! <a title="https://twitter.com/LBugnion" href="https://twitter.com/LBugnion">https://twitter.com/LBugnion</a> he&rsquo;s a great guy to chat with and very eager to help out anyone who needs help.</p>
<h2>&nbsp;</h2>
<h2>Conclusion</h2>
<p>MVVM Light is a great library with a few hidden gems. In this article I&rsquo;ve discussed 8 very interesting ones that can make your life as a developer easier. I&rsquo;ve included two more extra items because 10 is a prettier number than 8 <img class="wlEmoticon wlEmoticon-smile" src="http://www.spikie.be/blog/images/wlEmoticon-smile_18.png" alt="Glimlach" /></p>
<p>&nbsp;</p>
<p>&nbsp;</p>
<script type="text/javascript">// <![CDATA[
google_ad_client = "ca-pub-2343948435149147";
/* spikie.be underpost */
google_ad_slot = "9230774310";
google_ad_width = 468;
google_ad_height = 60;
// ]]
{% include imported_disclaimer.html %}
