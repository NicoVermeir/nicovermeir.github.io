---
layout: post
title: "branching xaml styler"
date: 2014-02-04 15:49:21 +0100
comments: true
published: true
categories: ["post"]
tags: [".Net", "Community", "Patterns", "Silverlight", "WP7", "WP8", "WPF", "WinRT", "Windows 8", "XAML", "github", "OSS"]
alias: ["/blog/post/2014/02/04/.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2014/02/04/.aspx
 - /blog/post/2014/02/04/.aspx
---
<p>Some time ago I <a href="http://www.spikie.be/blog/post/2013/11/13/.aspx" target="_blank">blogged</a> about having recompiled the great XAML Styler plugin so it would install into Visual Studio 2013. The recompiled plugin suddenly became an unexpected success and it got me thinking that this plugin should really continue to exist, even if the original author isn’t supporting it anymore (not sure of this but it has been quite some time since he pushed anything to Codeplex).</p>  <p>After waiting around a bit I’ve decided to download the original source code, get it up and running in Visual Studio 2013 and push it to both Github and the Visual Studio gallery. So I’m happy to announce that the code can be found at <a href="https://github.com/NicoVermeir/XamlStyler" target="_blank">Github</a> and the VS2013 version of the plugin can be found at the <a href="http://visualstudiogallery.msdn.microsoft.com/3de2a3c6-def5-42c4-924d-cc13a29ff5b7" target="_blank">Visual Studio Gallery</a></p>  <h2>The future?</h2>  <p>The version that is now on the gallery is the exact same version that I’ve compiled back in November. It’s the plugin that the original author created but recompiled with the VS2013 SDK. I did create a new VS Package project to be able to have it on the gallery side by side with the original plugin.</p>  <p>As for the future of the project, I’m currently going through the source code, getting to know the application and the code that makes it tick. Afterwards I’m planning on having a look at the logged issues on the original Codeplex site and tackle those. </p>  <h2>Issues</h2>  <p>Should you run into any issues while using the forked plugin please log an issue on the <a href="https://github.com/NicoVermeir/XamlStyler/issues" target="_blank">Github</a> page. </p>  <h2>Contributions</h2>  <p>So you want to contribute? Great! Fork the Github version and go wild! Create a Pull request when you’re ready and if everything checks out I’ll happily merge your changes into the main branch and give props to every contribution you make.</p>  <p>Let’s keep this plugin alive and make it even better together!</p>
{% include imported_disclaimer.html %}
