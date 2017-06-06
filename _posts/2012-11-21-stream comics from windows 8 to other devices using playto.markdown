---
layout: post
title: "stream comics from windows 8 to other devices using playto"
date: 2012-11-21 22:53:27 +0100
comments: true
published: true
categories: ["post"]
tags: [".Net", "Devices", "Metro", "WinRT", "Windows 8", "XAML"]
alias: ["/blog/post/2012/11/21/Stream-comics-from-Windows-8-to-other-devices-using-PlayTo.aspx", "/blog/post/2012/11/21/stream-comics-from-windows-8-to-other-devices-using-playto.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2012/11/21/Stream-comics-from-Windows-8-to-other-devices-using-PlayTo.aspx
 - /blog/post/2012/11/21/stream-comics-from-windows-8-to-other-devices-using-playto.aspx
---
<p>There are plenty of blogposts, tutorials, videos, books and many more out there that talk about searching, sharing settings and if you’re lucky even printing. But the Devices charm can be used for something way cooler than printing some pages, it can trigger the Play To contract.</p>  <p>The Play To contract can share media like music, video and pictures to other devices on your network, be it other Windows 8 devices or even Xbox 360 consoles. Being a comic geek I the first thing on my mind when reading about the Play To contract was “this would be awesome to stream comics from my pc to my Xbox” and so a challenge was born and accepted on the same day. An evening or two of hacking later I’m proud to say that I did it and it’s really easy, just like everything that involves charms (Microsoft really did a good job on allowing us to integrate our apps with the OS here). Most of the time putting this thing together went to building the actual comic viewer, but enough talk, let’s take a look at how it’s done. First let me show you how it looks like, we’ll start with the app itself.</p>  <p><a href="http://i50.tinypic.com/11iero0.jpg" target="_blank"><img style="float: none; margin-left: auto; display: block; margin-right: auto" src="http://i50.tinypic.com/11iero0.jpg" width="413" height="232" /></a></p>  <p>Pretty empty so far, if we load a digital comic (only .cbz format supported in this demo) and select the Devices charm we get this.</p>  <p><a href="http://i45.tinypic.com/35ic3fq.jpg" target="_blank"><img style="float: none; margin-left: auto; display: block; margin-right: auto" src="http://i45.tinypic.com/35ic3fq.jpg" width="419" height="236" /></a></p>  <p>That’s right! that’s Captain America himself, including Bucky. And oh yeah, the xbox shows up in the Devices charm as well. After selecting the xbox from the list of devices we get this.</p>  <p><a href="http://i50.tinypic.com/14b11e0.jpg" target="_blank"><img style="float: none; margin-left: auto; display: block; margin-right: auto" src="http://i50.tinypic.com/14b11e0.jpg" width="427" height="240" /></a></p>  <p>And here it is playing on my TV (yes that’s a Sony, because Microsoft doesn’t build televisions yet <img class="wlEmoticon wlEmoticon-winkingsmile" style="border-top-style: none; border-left-style: none; border-bottom-style: none; border-right-style: none" alt="Winking smile" src="http://www.spikie.be/blog/images/wlEmoticon-winkingsmile.png" />)</p>  <p><a href="http://i46.tinypic.com/2rqoec7.jpg" target="_blank"><img style="float: none; margin-left: auto; display: block; margin-right: auto" src="http://i46.tinypic.com/2rqoec7.jpg" width="432" height="324" /></a></p>  <p>Pretty cool eh? Let’s check under the hood.</p>  <p>First, a digital comic mostly exists in either .cbz or .cbr format. They’re actually nothing more then a zip or a rar file (Comic Book Zip and Comic Book Rar). Since WinRT has the ZipArchive class we can support .cbz out of the box, for cbr format we would need to find a library that supports rar files but that’s outside the scope of this post.</p>  <p>First the XAML, the main control here is a FlipView, that allows for touch and mouse support out of the box. The FlipView is bound to a collection of bitmap images that get loaded from the digital comic. Next to the FlipView there’s also an appbar containing the load file button and a textblock that shows the connection to other devices.</p>  <div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">   <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">     <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">&lt;</span><span style="color: #800000">common:LayoutAwarePage.BottomAppBar</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">AppBar</span> <span style="color: #ff0000">IsOpen</span><span style="color: #0000ff">=&quot;True&quot;</span> <span style="color: #ff0000">Background</span><span style="color: #0000ff">=&quot;#FF1A76B6&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">StackPanel</span> <span style="color: #ff0000">Orientation</span><span style="color: #0000ff">=&quot;Horizontal&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">Button</span> <span style="color: #ff0000">Click</span><span style="color: #0000ff">=&quot;OpenButton_Click&quot;</span> <span style="color: #ff0000">Style</span><span style="color: #0000ff">=&quot;{StaticResource OpenFileAppBarButtonStyle}&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">StackPanel</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">AppBar</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum7" style="color: #606060">   7:</span> <span style="color: #0000ff">&lt;/</span><span style="color: #800000">common:LayoutAwarePage.BottomAppBar</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum9" style="color: #606060">   9:</span> <span style="color: #0000ff">&lt;</span><span style="color: #800000">Grid</span> <span style="color: #ff0000">Style</span><span style="color: #0000ff">=&quot;{StaticResource LayoutRootStyle}&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">Grid.RowDefinitions</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum11" style="color: #606060">  11:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">RowDefinition</span> <span style="color: #ff0000">Height</span><span style="color: #0000ff">=&quot;140&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum12" style="color: #606060">  12:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">RowDefinition</span> <span style="color: #ff0000">Height</span><span style="color: #0000ff">=&quot;*&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum13" style="color: #606060">  13:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Grid.RowDefinitions</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum14" style="color: #606060">  14:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum15" style="color: #606060">  15:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">TextBlock</span> <span style="color: #ff0000">x:Name</span><span style="color: #0000ff">=&quot;ConnectionText&quot;</span> <span style="color: #ff0000">TextWrapping</span><span style="color: #0000ff">=&quot;Wrap&quot;</span> <span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;Not connected&quot;</span> <span style="color: #ff0000">Margin</span><span style="color: #0000ff">=&quot;38,18,766,571&quot;</span> </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum16" style="color: #606060">  16:</span>                <span style="color: #ff0000">Grid</span>.<span style="color: #ff0000">Row</span><span style="color: #0000ff">=&quot;1&quot;</span> <span style="color: #ff0000">Style</span><span style="color: #0000ff">=&quot;{StaticResource SubheaderTextStyle}&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum17" style="color: #606060">  17:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum18" style="color: #606060">  18:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">FlipView</span> <span style="color: #ff0000">x:Name</span><span style="color: #0000ff">=&quot;FlipImage&quot;</span> <span style="color: #ff0000">Margin</span><span style="color: #0000ff">=&quot;0,3,0,0&quot;</span> <span style="color: #ff0000">Grid</span>.<span style="color: #ff0000">RowSpan</span><span style="color: #0000ff">=&quot;2&quot;</span> <span style="color: #ff0000">SelectionChanged</span><span style="color: #0000ff">=&quot;FlipImage_NextImage&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum19" style="color: #606060">  19:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">FlipView.ItemTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum20" style="color: #606060">  20:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">DataTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum21" style="color: #606060">  21:</span>                 <span style="color: #0000ff">&lt;</span><span style="color: #800000">Image</span> <span style="color: #ff0000">Source</span><span style="color: #0000ff">=&quot;{Binding}&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum22" style="color: #606060">  22:</span>             <span style="color: #0000ff">&lt;/</span><span style="color: #800000">DataTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum23" style="color: #606060">  23:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">FlipView.ItemTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum24" style="color: #606060">  24:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">FlipView</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum25" style="color: #606060">  25:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum26" style="color: #606060">  26:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">Grid</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum27" style="color: #606060">  27:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">Grid.ColumnDefinitions</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum28" style="color: #606060">  28:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">ColumnDefinition</span> <span style="color: #ff0000">Width</span><span style="color: #0000ff">=&quot;Auto&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum29" style="color: #606060">  29:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">ColumnDefinition</span> <span style="color: #ff0000">Width</span><span style="color: #0000ff">=&quot;*&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum30" style="color: #606060">  30:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Grid.ColumnDefinitions</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum31" style="color: #606060">  31:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">Button</span> <span style="color: #ff0000">x:Name</span><span style="color: #0000ff">=&quot;backButton&quot;</span> <span style="color: #ff0000">Click</span><span style="color: #0000ff">=&quot;GoBack&quot;</span> <span style="color: #ff0000">IsEnabled</span><span style="color: #0000ff">=&quot;{Binding Frame.CanGoBack, ElementName=pageRoot}&quot;</span> </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum32" style="color: #606060">  32:</span>                 <span style="color: #ff0000">Style</span><span style="color: #0000ff">=&quot;{StaticResource BackButtonStyle}&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum33" style="color: #606060">  33:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">TextBlock</span> <span style="color: #ff0000">x:Name</span><span style="color: #0000ff">=&quot;pageTitle&quot;</span> <span style="color: #ff0000">Grid</span>.<span style="color: #ff0000">Column</span><span style="color: #0000ff">=&quot;1&quot;</span> <span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;{StaticResource AppName}&quot;</span> </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum34" style="color: #606060">  34:</span>                    <span style="color: #ff0000">Style</span><span style="color: #0000ff">=&quot;{StaticResource PageHeaderTextStyle}&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum35" style="color: #606060">  35:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Grid</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum36" style="color: #606060">  36:</span> <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Grid</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF--></div>
</div>

<p>The FlipView template is just a simple Image control, nothing special in the XAML, the magic is in the code behind.</p>

<p>First some fields that we’ll need later on</p>

<div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">
    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">private</span> List&lt;BitmapImage&gt; _pages;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> <span style="color: #0000ff">private</span> <span style="color: #0000ff">bool</span> _isConnected;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span> <span style="color: #0000ff">private</span> Image _current;</pre>
<!--CRLF--></div>
</div>

<p>We’re going to take a look at how to load the cbz file first, I’ll go over this quickly as the main focus of this post is the PlayTo contract.</p>

<div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">
    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">private</span> async <span style="color: #0000ff">void</span> OpenButton_Click(<span style="color: #0000ff">object</span> sender, RoutedEventArgs e)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     _pages = <span style="color: #0000ff">new</span> List&lt;BitmapImage&gt;();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>     _pages = await OpenZip();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>     <span style="color: #0000ff">if</span> (_pages.Count &gt; 0)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>         FlipImage.ItemsSource = _pages;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum9" style="color: #606060">   9:</span> }</pre>
<!--CRLF--></div>
</div>

<p>All we do in the button’s eventhandler is initializing the field that will hold all the pages, call the function that will load the file and if it contains any items it will set the FlipView’s itemssource to that list. Next up: the function to load the comic.</p>

<div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">
    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> async Task&lt;List&lt;BitmapImage&gt;&gt; OpenZip()</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     FileOpenPicker openPicker = <span style="color: #0000ff">new</span> FileOpenPicker();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>     List&lt;BitmapImage&gt; comic = <span style="color: #0000ff">new</span> List&lt;BitmapImage&gt;();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>     openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>     openPicker.FileTypeFilter.Add(<span style="color: #006080">&quot;.cbz&quot;</span>);</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum9" style="color: #606060">   9:</span>     var storageFile = await openPicker.PickSingleFileAsync();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span>     <span style="color: #008000">// Create stream for compressed files in memory</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum11" style="color: #606060">  11:</span>     <span style="color: #0000ff">using</span> (MemoryStream zipMemoryStream = <span style="color: #0000ff">new</span> MemoryStream())</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum12" style="color: #606060">  12:</span>     {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum13" style="color: #606060">  13:</span>         <span style="color: #0000ff">using</span> (IRandomAccessStream zipStream = await storageFile.OpenAsync(FileAccessMode.Read))</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum14" style="color: #606060">  14:</span>         {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum15" style="color: #606060">  15:</span>             <span style="color: #008000">// Read compressed data from file to memory stream</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum16" style="color: #606060">  16:</span>             <span style="color: #0000ff">using</span> (Stream instream = zipStream.AsStreamForRead())</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum17" style="color: #606060">  17:</span>             {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum18" style="color: #606060">  18:</span>                 <span style="color: #0000ff">byte</span>[] buffer = <span style="color: #0000ff">new</span> <span style="color: #0000ff">byte</span>[1024];</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum19" style="color: #606060">  19:</span>                 <span style="color: #0000ff">while</span> (instream.Read(buffer, 0, buffer.Length) &gt; 0)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum20" style="color: #606060">  20:</span>                 {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum21" style="color: #606060">  21:</span>                     zipMemoryStream.Write(buffer, 0, buffer.Length);</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum22" style="color: #606060">  22:</span>                 }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum23" style="color: #606060">  23:</span>             }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum24" style="color: #606060">  24:</span>         }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum25" style="color: #606060">  25:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum26" style="color: #606060">  26:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum27" style="color: #606060">  27:</span>         <span style="color: #008000">// Create zip archive to access compressed files in memory stream</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum28" style="color: #606060">  28:</span>         <span style="color: #0000ff">using</span> (ZipArchive zipArchive = <span style="color: #0000ff">new</span> ZipArchive(zipMemoryStream, ZipArchiveMode.Read))</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum29" style="color: #606060">  29:</span>         {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum30" style="color: #606060">  30:</span>             <span style="color: #008000">// For each compressed file...</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum31" style="color: #606060">  31:</span>             <span style="color: #0000ff">foreach</span> (ZipArchiveEntry item <span style="color: #0000ff">in</span> zipArchive.Entries)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum32" style="color: #606060">  32:</span>             {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum33" style="color: #606060">  33:</span>                 <span style="color: #0000ff">if</span> (item.Name.EndsWith(<span style="color: #006080">&quot;.jpg&quot;</span>, StringComparison.OrdinalIgnoreCase))</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum34" style="color: #606060">  34:</span>                 {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum35" style="color: #606060">  35:</span>                     <span style="color: #0000ff">byte</span>[] imageInBytes;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum36" style="color: #606060">  36:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum37" style="color: #606060">  37:</span>                     <span style="color: #0000ff">using</span> (MemoryStream ms = <span style="color: #0000ff">new</span> MemoryStream())</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum38" style="color: #606060">  38:</span>                     {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum39" style="color: #606060">  39:</span>                         var stream = item.Open();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum40" style="color: #606060">  40:</span>                         stream.CopyTo(ms);</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum41" style="color: #606060">  41:</span>                         imageInBytes = ms.ToArray();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum42" style="color: #606060">  42:</span>                     }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum43" style="color: #606060">  43:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum44" style="color: #606060">  44:</span>                     BitmapImage bImg = <span style="color: #0000ff">new</span> BitmapImage();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum45" style="color: #606060">  45:</span>                     await bImg.SetSourceAsync(<span style="color: #0000ff">new</span> RandomStream(imageInBytes));</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum46" style="color: #606060">  46:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum47" style="color: #606060">  47:</span>                     comic.Add(bImg);</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum48" style="color: #606060">  48:</span>                 }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum49" style="color: #606060">  49:</span>             }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum50" style="color: #606060">  50:</span>         }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum51" style="color: #606060">  51:</span>     }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum52" style="color: #606060">  52:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum53" style="color: #606060">  53:</span>     <span style="color: #0000ff">return</span> comic;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum54" style="color: #606060">  54:</span> }</pre>
<!--CRLF--></div>
</div>

<p>We start by initializing the FileOpenPicker, allowing our user to select the comic he/she wants to read. We add a suggested location where the FileOpenPicker should start and add the filetypes it should look for. The PickFileAsync method shows the actual filepicker to the user, the user selects the cbz file he wants and it gets loaded into the storageFile variable. The file gets read in as an IRandomAccessStream. We need that stream to create a ZipArchive instance. Once we have that we can loop through all files in that zip archive. Each .jpg file in that zip archive gets read into a byte array that we then convert into a bitmap by using RandomStream, an implementation of IRandomAccessStream (if you want to see the implementation, the project is attached to this post at the bottom). The bitmap image then gets added to the list. When they’re all done the list gets returned to the caller.</p>

<p>That’s it for loading the comic, let’s take a look at the sharing to other devices in your network.</p>

<p>We need to initialize the PlayTo contract, I’ll be doing this from the constructor</p>

<div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">
    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">public</span> MainPage()</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     InitializeComponent();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>     var playToManager = PlayToManager.GetForCurrentView();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>     playToManager.SourceRequested += PlayToManagerOnSourceRequested;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>     playToManager.SourceSelected += PlayToManagerOnSourceSelected;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span> }</pre>
<!--CRLF--></div>
</div>

<p>PlayToManager is the class that we need, we get this for free from the WinRT framework. The GetForCurrentView() method returns an instance of the PlayToManager class bound to this page. Once we have the instance we attach an eventhandler to the SourceRequested and the SourceSelected events. The SourceRequested event will fire as soon as the user hits the Devices charm, this is where we’ll prepare the first media element for streaming. The SourceSelected event fires when the user selects a source, obviously.</p>

<div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">
    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">private</span> async <span style="color: #0000ff">void</span> PlayToManagerOnSourceRequested(PlayToManager sender, PlayToSourceRequestedEventArgs args)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     var deferral = args.SourceRequest.GetDeferral();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =&gt;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>         {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>             var firstImage = GetChildren(FlipImage).First();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum9" style="color: #606060">   9:</span>             <span style="color: #008000">// Provide Play To with the first image to stream.</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span>             args.SourceRequest.SetSource(firstImage.PlayToSource);</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum11" style="color: #606060">  11:</span>             _current = firstImage;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum12" style="color: #606060">  12:</span>             deferral.Complete();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum13" style="color: #606060">  13:</span>         });</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum14" style="color: #606060">  14:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum15" style="color: #606060">  15:</span> }</pre>
<!--CRLF--></div>
</div>

<p>The OnSourceRequested eventhandler needs to be marked as async. First we get the deferral, then we need to run some async code, Dispatcher.RunAsync is the same as calling an async method with await on this line. The PlayTo contract works with certain XAML controls. In our case we need the Image control, that’s why we’ve set the itemtemplate of the FlipView to be an Image. We’ll take a look at the GetChildren() method in a minute, for now just know that it returns a list of all Image controls inside the FlipView. We take the first element in the returned list and that’s the element that we’ll stream to the device. The arguments have a property of type PlayToSourceRequest, that one has a SetSource function that takes in a PlayToSource object and that’s a property of the Image control. We set the current image to the _current field and mark the deferral as complete.</p>

<p>Phew that was quite some work. Don’t worry, the hard part is over (yes this was really the hard part). Now a quick look at that GetChildren() function.</p>

<div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">
    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">private</span> List&lt;Image&gt; GetChildren(DependencyObject parent)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     var list = <span style="color: #0000ff">new</span> List&lt;Image&gt;();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>     <span style="color: #0000ff">for</span> (<span style="color: #0000ff">int</span> i = 0; i &lt; VisualTreeHelper.GetChildrenCount(parent); i++)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>     {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>         var child = VisualTreeHelper.GetChild(parent, i);</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>         var item = child <span style="color: #0000ff">as</span> Image;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum9" style="color: #606060">   9:</span>         <span style="color: #0000ff">if</span> (item != <span style="color: #0000ff">null</span>)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span>             list.Add(item);</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum11" style="color: #606060">  11:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum12" style="color: #606060">  12:</span>         list.AddRange(GetChildren(child));</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum13" style="color: #606060">  13:</span>     }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum14" style="color: #606060">  14:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum15" style="color: #606060">  15:</span>     <span style="color: #0000ff">return</span> list;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum16" style="color: #606060">  16:</span> }</pre>
<!--CRLF--></div>
</div>

<p>The function takes in a DependencyObject and starts walking through its visual tree. We try to cast each item as an Image, if that cast fails the variable will contain null, a quick check there and if it isn’t null we add it to the list which we then return.</p>

<p>The OnSourceSelected is only used to set the name of the selected source to the textblock</p>

<div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">
    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">private</span> async <span style="color: #0000ff">void</span> PlayToManagerOnSourceSelected(PlayToManager sender, PlayToSourceSelectedEventArgs args)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     _isConnected = <span style="color: #0000ff">true</span>;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =&gt;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>         ConnectionText.Text = <span style="color: #0000ff">string</span>.Format(<span style="color: #006080">&quot;Connected to {0}&quot;</span>, args.FriendlyName));</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum7" style="color: #606060">   7:</span> }</pre>
<!--CRLF--></div>
</div>

<p>And that’s enough to stream the first image, when you run this code and select the Devices charm the first page of the comic should show up on your device. All there’s left now is to go back and forward in the comic. In the app itself this already works, the FlipView takes care of navigating between the pages. Before we start developing this we need to make a small halt and take a look at how the FlipView actually works. First thought in my head was “okay this is easy, I just get all the image controls in the FlipView and I’m golden”. That was a big nono. The itemspanel of a FlipView is actually a VirtualizingStackPanel, meaning that at any given time there are maximum three Image controls inside the FlipView, usually previous-current-next. As soon as we navigate to another page the FlipView automatically loads in the next item in line. This can easily be seen by using a handy tool called XamlSpy. XamlSpy allows us to view the entire visual tree of any XAML based application. When we view the default visual tree of a FlipView after loading a comic we get this.</p>

<p><img style="float: none; margin-left: auto; display: block; margin-right: auto" src="http://i49.tinypic.com/2gt0n69.jpg" /></p>

<p>As you can see, we only have three FlipViewItems here. When we change the FlipView’s paneltemplate to this</p>

<div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">
    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">&lt;</span><span style="color: #800000">FlipView</span> <span style="color: #ff0000">x:Name</span><span style="color: #0000ff">=&quot;FlipImage&quot;</span> <span style="color: #ff0000">Margin</span><span style="color: #0000ff">=&quot;0,3,0,0&quot;</span> <span style="color: #ff0000">Grid</span>.<span style="color: #ff0000">RowSpan</span><span style="color: #0000ff">=&quot;2&quot;</span> <span style="color: #ff0000">SelectionChanged</span><span style="color: #0000ff">=&quot;FlipImage_NextImage&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">FlipView.ItemsPanel</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">ItemsPanelTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">StackPanel</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">ItemsPanelTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">FlipView.ItemsPanel</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">FlipView.ItemTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">DataTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum9" style="color: #606060">   9:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">Image</span> <span style="color: #ff0000">Source</span><span style="color: #0000ff">=&quot;{Binding}&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">DataTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum11" style="color: #606060">  11:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">FlipView.ItemTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum12" style="color: #606060">  12:</span> <span style="color: #0000ff">&lt;/</span><span style="color: #800000">FlipView</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF--></div>
</div>

<p>and we load the same comic in XamlSpy we get this result</p>

<p><img style="float: none; margin-left: auto; display: block; margin-right: auto" src="http://i46.tinypic.com/rhogsg.jpg" /></p>

<p>Quite the difference I would say. The VirtualizingStackPanel is lighter on memory usage, since some comics can be quite large we’ll stick to the default template.</p>

<p>Now that we have that cleared out, let’s take a look at what happens when we browse to the next page of the comic.</p>







<div id="codeSnippetWrapper" style="overflow: auto; cursor: text; font-size: 8pt; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; margin: 20px 0px 10px; border-left: silver 1px solid; line-height: 12pt; padding-right: 4px; max-height: 200px; width: 97.5%; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4">
    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">private</span> async <span style="color: #0000ff">void</span> FlipImage_NextImage(<span style="color: #0000ff">object</span> sender, SelectionChangedEventArgs e)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     <span style="color: #0000ff">if</span> (!_isConnected) <span style="color: #0000ff">return</span>;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =&gt;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>         {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>             var current = GetChildren(FlipImage)[1];</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum9" style="color: #606060">   9:</span>             _current.PlayToSource.Next = current.PlayToSource;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span>             _current.PlayToSource.PlayNext();</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum11" style="color: #606060">  11:</span>             _current = current;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: #f4f4f4"><span id="lnum12" style="color: #606060">  12:</span>         });</pre>
<!--CRLF-->

    <pre style="border-top-style: none; overflow: visible; font-size: 8pt; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; width: 100%; background-color: white"><span id="lnum13" style="color: #606060">  13:</span> }</pre>
<!--CRLF--></div>
</div>

<p>First, this code does not need to be executed when we’re not connected to any device, if we are we need to run the next block of code asynchronously. First the code fetches all the available Image controls inside the FlipView, we save a reference to the middle one because that one is the one currently shown in the FlipView. The field _current contains the image currently shown on the external device, we need to set that field’s PlayToSource.Next property. That property always needs to be set on the current image before the PlayNext() method is called. Once that’s set we call the aforementioned PlayNext() method. That method will sent the control that is set to the PlayToSource.Next property to the connected device. To end we set the image control that was just send to the device to the _current field so that it can be called upon on the next run.</p>







<p>In this post I have shown how you can share media content from your Windows Store application to an external device like the Xbox 360. The project used in this post can be downloaded from my <a href="http://sdrv.ms/10l0T6w" target="_blank">SkyDrive</a></p>
{% include imported_disclaimer.html %}
