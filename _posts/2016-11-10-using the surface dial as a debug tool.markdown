---
layout: post
title: "using the surface dial as a debug tool"
date: 2016-11-10 23:02:13 +0100
comments: true
published: true
categories: ["post"]
tags: []
alias: ["/blog/post/2016/11/10/.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2016/11/10/.aspx
 - /blog/post/2016/11/10/.aspx
---
<p>Update: here’s a small video of the extension in action</p>  <p><iframe height="315" src="https://www.youtube.com/embed/FxdQ5XwYBFI" frameborder="0" width="560" allowfullscreen="allowfullscreen"></iframe></p>  <p>&#160;</p>  <p>The Surface Dial is a nifty little device, it feels great and can add some nice capabilities to your apps. However, the very best thing about it is that is has an API. I had a go with it and decided to try and turn the dial into a debugging device.</p>  <h2>The goal</h2>  <p>The Dial has a few gestures. It’s a button that you can press, and it rotates clockwise and counter-clockwise. My goal was to start a debugging session by clicking the Dial, Step-Over (F10) by rotating clockwise and Step-Into (F11) by rotating counter-clockwise.</p>  <h2>Pre-requisites</h2>  <p>We’ll need some stuff to get going:</p>  <ul>   <li><a href="https://www.microsoftstore.com/store/msusa/en_US/pdp/Surface-Dial/productID.5074013900" target="_blank">Surface Dial</a> </li>    <li><a href="https://www.visualstudio.com/post-download-vs/?sku=community&amp;clcid=0x409&amp;telem=ga" target="_blank">Visual Studio 2015 (any edition is fine)</a> </li>    <li>Visual Studio 2015 SDK (included in the Visual Studio Installer) </li>    <li>Windows 10 Anniversary Update or later </li> </ul>  <h2>Setting up a Visual Studio Extension</h2>  <p>To start creating a Visual Studio Extension, launch Visual Studio 2015 and start a new project. If the SDK is installed correctly there should be an Extensibility option with a VSIX Project template.</p>  <p><img src="http://i67.tinypic.com/250tlyf.jpg" width="646" height="449" /></p>  <p>The project it creates is basically an empty box, just some boilerplate code to have a VSIX installer and have it install your extension in VS. To start adding some functionality to the extension we need to add an extra item, in this case it will be a package, these are added as a new item into the project.</p>  <p><img src="http://i64.tinypic.com/mqbd0.jpg" width="644" height="447" /></p>  <p>Almost ready, all we need to add next is when our extension should load. We’re going to choose for load on solution load in this sample. This is done by adding an attribute on the package class.</p>  <p><script src="https://gist.github.com/NicoVermeir/a8eae0005bdb86acae0ab9b50abb40fe.js"></script></p>  <p>Thrown together, we get:</p>  <p><script src="https://gist.github.com/NicoVermeir/f38a5f2d8ef54c9d6ad7fb760ea0b825.js"></script></p>  <pre> </pre>

<p>If you press F5 now, an experimental instance of Visual Studio should launch, with the extension installed.</p>

<h2>Connecting to the Dial</h2>

<p>This was the hardest part of the project, the Dial API is done in UWP. This is how we can grab the Dial instance in UWP (currently only one dial is supported per system)</p>

<pre><em>var controller = RadialController.CreateForCurrentView();</em></pre>

<p>The problem here is not the fact that it’s a UWP API, </font><a href="https://mtaulty.com/2016/10/11/calling-uwp-apis-from-a-desktop-application/" target="_blank">we can use those from win32</a>. The problem is that the CreateForCurrentView() method requires a handle to the current window, a UWP window, which we obviously don’t have. I found the solution for this in one of the official Microsoft samples. They have a sample in the windows-classic-samples repository that shows <a href="https://github.com/Microsoft/Windows-classic-samples/tree/master/Samples/RadialController/cs/winforms" target="_blank">how to access the Dial from a WinForms application</a>.</p>

<p>I took the RadialControllerInterop class from that sample and added it to the extension. however, as it turned out, it was only part of the solution.</p>

<p><script src="https://gist.github.com/NicoVermeir/51aa2e836ea7b20887439459e5406b81.js"></script></p>

<p>The interop interfaces actually reference the RadialController class that lives in Windows.UI.Input, a UWP namespace that we currently have no reference to. That’s where the <a href="https://github.com/ljw1004/uwp-desktop/blob/master/README.md" target="_blank">UwpDesktop</a> NuGet package comes in. This package makes it easy to reference UWP classes from win32 style applications. Once added through NuGet we can add the Windows.UI.Input namespace to the interop class.</p>

<p><script src="https://gist.github.com/NicoVermeir/88bc47f259e052a193fb9d1fe10f4e8b.js"></script></p>

<p>We’ve got everything in place to connect to the Dial from Visual Studio.</p>

<h2>Glueing it together</h2>

<p>Back to the package class, there should be an Initialize method, overridden from the base class. This is where we’ll hook everything up.</p>

<p><script src="https://gist.github.com/NicoVermeir/fa5ff8ba6e8fbecec15e48c9ca36a65a.js"></script></p>

<p>First thing we’ll do is grab hold of the current Visual Studio instance, which is a DTE object. We keep a reference to this object in a field because we’ll need it later.</p>

<p>Next up is connecting to the Dial</p>

<p><script src="https://gist.github.com/NicoVermeir/fcb75a1e93fcd0eac131f46fabddcac1.js"></script></p>

<p>In this method we’re using interop to get access to the UWP RadialController class via the interface we took from the classic windows samples. As a handle for the windows we pass in the handle for our current Visual Studio instance which we can get to from the DTE object. At this point we got a reference to the Dial that is connected to our system.</p>

<p>The Dial has a radial menu that we can hook into and add or remove items from it. We’ll add a Debug item to it.</p>

<p><script src="https://gist.github.com/NicoVermeir/f5235fbec2a58f6ac005008d805ac3d0.js"></script></p>

<p>I’m doing this through a list just in case I ever want to add more items, but at the moment I’m only adding one. A RadialControllerMenuItem is created via the CreateFromKnownIcon method. First parameter is the text that will be displayed in the menu, the second one is an enum value from an enum that contains some known menu icons. A RadialController has a menu property that has a collection of items, all we need to do is add our items to that collection and it will show up in the radial menu.</p>

<p><img src="http://i67.tinypic.com/2ng9afo.jpg" /></p>

<p>Final step is adding the event handlers and calling the debugger methods to let the Dial step through the code.</p>

<p><script src="https://gist.github.com/NicoVermeir/433632420bb4c347ede919c8a6e51065.js"></script></p>

<p>As you can see, the events coming from the dial are very straightforward, there’s a ButtonClicked and a RotationChanged event.</p>

<p>Here’s what we need to do to start debugging on button click:</p>

<p><script src="https://gist.github.com/NicoVermeir/00409ee73e8c96238be1618832758fb9.js"></script></p>

<p>The Visual Studio SDK provides an easy to use API surface. We can use the DTE.Application.Debugger class to control the debugger. The Go() method launches a debug session, the Stop() method, well, stops a debugging session.</p>

<p>Next, we’ll handle the rotation events</p>

<p><script src="https://gist.github.com/NicoVermeir/9db3bc0a8c27934dda28a29b4e6a6e3a.js"></script></p>

<p>From the args we get the rotation delta, one step on the rotator is a delta of 10, +10 clockwise and –10 counter-clockwise.</p>

<p>And that’s it, build it in release mode and the bin/release folder will contain a .vsix installer file. Close VS, install the extension, reopen VS, open a solution, select Debug from the radial menu and debug away!</p>

<p>The source code is on <a href="https://github.com/NicoVermeir/DialDebug" target="_blank">GitHub</a>. Here’s a link to the <a href="https://github.com/NicoVermeir/DialDebug/blob/master/DialDebug/DialDebug.zip" target="_blank">compiled VSIX file.</a></p>

<p>Disclaimer: the code is provided as-is. I do not plan to publish this on the gallery or maintain the project. Do feel free to pick this up and create an open-source project from it (would be nice to include a reference to this post in the description <img class="wlEmoticon wlEmoticon-smile" style="border-top-style: none; border-left-style: none; border-bottom-style: none; border-right-style: none" alt="Smile" src="http://www.spikie.be/blog/images/wlEmoticon-smile_34.png" />)</p>

<p>happy coding!</p>
{% include imported_disclaimer.html %}
