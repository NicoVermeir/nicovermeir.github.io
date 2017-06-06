---
layout: post
title: "xamarin forms bindable wrappanel"
date: 2015-04-02 20:44:32 +0200
comments: true
published: true
categories: ["post"]
tags: [".Net", "Xamarin", "XAML", "WinRT"]
alias: ["/blog/post/2015/04/02/.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2015/04/02/.aspx
 - /blog/post/2015/04/02/.aspx
---
<p>A wrappanel is something XAML developers are very familiar with. It’s a panel that orders its children either horizontally or vertically but when the children reach the border, the panel wraps to the next row or column. </p>  <p>Xamarin Forms currently does not have a default implementation for something similar so I set out to find one online. To my surprise it took quite some time to find something (guess I’m spoiled by the awesome toolkits we have as Windows app devs), eventually I found the <a href="https://github.com/conceptdev/xamarin-forms-samples/blob/145a43fb6153fc069eeb99d86f0e219ad6d8fcac/Evolve13/Evolve13/Controls/WrapLayout.cs" target="_blank">WrapLayout.</a></p>  <p>The WrapLayout works but had a disadvantage, it didn’t have an ItemsSource or DataTemplate property but I did have a solid foundation, so I went to expand the implementation.</p>  <p>I started by creating a bindable property for itemssource and one for item template. In MS XAML we would use Dependency properties, in Xamarin XAML it’s called BindableProperty.</p>  <div id="codeSnippetWrapper" style="font-size: 8pt; overflow: auto; cursor: text; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; width: 97.5%; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; border-left: silver 1px solid; margin: 20px 0px 10px; line-height: 12pt; padding-right: 4px; max-height: 200px; background-color: #f4f4f4">   <div id="codeSnippet" style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4">     <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #008000">/// &lt;summary&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> <span style="color: #008000">/// Backing Storage for the Spacing property</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum3" style="color: #606060">   3:</span> <span style="color: #008000">/// &lt;/summary&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span> <span style="color: #0000ff">public</span> <span style="color: #0000ff">static</span> <span style="color: #0000ff">readonly</span> BindableProperty ItemTemplateProperty =</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>     BindableProperty.Create&lt;AwesomeWrappanel, DataTemplate&gt;(w =&gt; w.ItemTemplate, <span style="color: #0000ff">null</span>,</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>         propertyChanged: (bindable, oldvalue, newvalue) =&gt; ((AwesomeWrappanel)bindable).OnSizeChanged());</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span> <span style="color: #008000">/// &lt;summary&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum9" style="color: #606060">   9:</span> <span style="color: #008000">/// Spacing added between elements (both directions)</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span> <span style="color: #008000">/// &lt;/summary&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum11" style="color: #606060">  11:</span> <span style="color: #008000">/// &lt;value&gt;The spacing.&lt;/value&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum12" style="color: #606060">  12:</span> <span style="color: #0000ff">public</span> DataTemplate ItemTemplate</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum13" style="color: #606060">  13:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum14" style="color: #606060">  14:</span>     get { <span style="color: #0000ff">return</span> (DataTemplate)GetValue(ItemTemplateProperty); }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum15" style="color: #606060">  15:</span>     set { SetValue(ItemTemplateProperty, <span style="color: #0000ff">value</span>); }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum16" style="color: #606060">  16:</span> }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum17" style="color: #606060">  17:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum18" style="color: #606060">  18:</span> <span style="color: #008000">/// &lt;summary&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum19" style="color: #606060">  19:</span> <span style="color: #008000">/// Backing Storage for the Spacing property</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum20" style="color: #606060">  20:</span> <span style="color: #008000">/// &lt;/summary&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum21" style="color: #606060">  21:</span> <span style="color: #0000ff">public</span> <span style="color: #0000ff">static</span> <span style="color: #0000ff">readonly</span> BindableProperty ItemsSourceProperty =</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum22" style="color: #606060">  22:</span>     BindableProperty.Create&lt;AwesomeWrappanel, IEnumerable&gt;(w =&gt; w.ItemsSource, <span style="color: #0000ff">null</span>,</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum23" style="color: #606060">  23:</span>         propertyChanged: ItemsSource_OnPropertyChanged);</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum24" style="color: #606060">  24:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum25" style="color: #606060">  25:</span> <span style="color: #008000">/// &lt;summary&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum26" style="color: #606060">  26:</span> <span style="color: #008000">/// Spacing added between elements (both directions)</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum27" style="color: #606060">  27:</span> <span style="color: #008000">/// &lt;/summary&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum28" style="color: #606060">  28:</span> <span style="color: #008000">/// &lt;value&gt;The spacing.&lt;/value&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum29" style="color: #606060">  29:</span> <span style="color: #0000ff">public</span> IEnumerable ItemsSource</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum30" style="color: #606060">  30:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum31" style="color: #606060">  31:</span>     get { <span style="color: #0000ff">return</span> (IEnumerable)GetValue(ItemsSourceProperty); }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum32" style="color: #606060">  32:</span>     set { SetValue(ItemsSourceProperty, <span style="color: #0000ff">value</span>); }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum33" style="color: #606060">  33:</span> }</pre>
<!--CRLF--></div>
</div>

<p>ItemTemplate is a property of type DataTemplate, ItemSource is an IEnumerable (the non-generic version). So far so good, next up is adding some logic to add the itemsource items as children of the Layout. Since the wrappanel has Layout&lt;T&gt; as a base there is no itemsource from the underlying control.</p>

<div id="codeSnippetWrapper" style="font-size: 8pt; overflow: auto; cursor: text; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; width: 97.5%; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; border-left: silver 1px solid; margin: 20px 0px 10px; line-height: 12pt; padding-right: 4px; max-height: 200px; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4">
    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">private</span> <span style="color: #0000ff">void</span> OnCollectionChanged(<span style="color: #0000ff">object</span> sender, NotifyCollectionChangedEventArgs args)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     <span style="color: #0000ff">foreach</span> (<span style="color: #0000ff">object</span> item <span style="color: #0000ff">in</span> args.NewItems)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>     {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>         var child = ItemTemplate.CreateContent() <span style="color: #0000ff">as</span> View;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>         <span style="color: #0000ff">if</span> (child == <span style="color: #0000ff">null</span>)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>             <span style="color: #0000ff">return</span>;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum9" style="color: #606060">   9:</span>         child.BindingContext = item;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span>         Children.Add(child);</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum11" style="color: #606060">  11:</span>     }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum12" style="color: #606060">  12:</span> }</pre>
<!--CRLF--></div>
</div>

<p>We’re going to respond to collection changed events, hoping that whoever uses the control binds to a collection that implements INotifyCollectionChanged, like the infamous ObservableCollection&lt;T&gt;. We’re creating a View from the DataTemplate for every newly added item, set the BindingContext to the item itself and add it to the Children collection. The binding framework will take care of all the rest.</p>

<p>One step left, we need to add an event handler for the CollectionChanged event. We do this in the property changed callback of the ItemSource bindable property</p>

<div id="codeSnippetWrapper" style="font-size: 8pt; overflow: auto; cursor: text; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; width: 97.5%; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; border-left: silver 1px solid; margin: 20px 0px 10px; line-height: 12pt; padding-right: 4px; max-height: 200px; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4">
    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">private</span> <span style="color: #0000ff">static</span> <span style="color: #0000ff">void</span> ItemsSource_OnPropertyChanged(BindableObject bindable, IEnumerable oldvalue, IEnumerable newvalue)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     <span style="color: #0000ff">if</span> (oldvalue != <span style="color: #0000ff">null</span>)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>     {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>         var coll = (INotifyCollectionChanged)oldvalue;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>         <span style="color: #008000">// Unsubscribe from CollectionChanged on the old collection</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>         coll.CollectionChanged -= ItemsSource_OnItemChanged;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>     }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum9" style="color: #606060">   9:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span>     <span style="color: #0000ff">if</span> (newvalue != <span style="color: #0000ff">null</span>)</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum11" style="color: #606060">  11:</span>     {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum12" style="color: #606060">  12:</span>         var coll = (INotifyCollectionChanged)newvalue;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum13" style="color: #606060">  13:</span>         <span style="color: #008000">// Subscribe to CollectionChanged on the new collection</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum14" style="color: #606060">  14:</span>         coll.CollectionChanged += ItemsSource_OnItemChanged;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum15" style="color: #606060">  15:</span>     }</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum16" style="color: #606060">  16:</span> }</pre>
<!--CRLF--></div>
</div>

<p>If there’s a previous instance we disconnect from its event, afterwards we try to cast the itemsource to INotifyCollectionChanged, if that succeeds we know there’s a collection changed event we can hook into. One problem here, this is all static but the properties we need to use in the event handler are not so we need to find a way to get from static to non-static code, events to the rescue!</p>

<p>I added a static event, cool thing about those is that the event handlers can be non-static.</p>

<div id="codeSnippetWrapper" style="font-size: 8pt; overflow: auto; cursor: text; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; width: 97.5%; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; border-left: silver 1px solid; margin: 20px 0px 10px; line-height: 12pt; padding-right: 4px; max-height: 200px; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4">
    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">private</span> <span style="color: #0000ff">static</span> <span style="color: #0000ff">event</span> EventHandler&lt;NotifyCollectionChangedEventArgs&gt; _collectionChanged;</pre>
<!--CRLF--></div>
</div>

<p>Used the constructor to hook up the handler</p>

<div id="codeSnippetWrapper" style="font-size: 8pt; overflow: auto; cursor: text; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; width: 97.5%; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; border-left: silver 1px solid; margin: 20px 0px 10px; line-height: 12pt; padding-right: 4px; max-height: 200px; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4">
    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">public</span> AwesomeWrappanel()</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span> {</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>     _collectionChanged += OnCollectionChanged;</pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span> }</pre>
<!--CRLF--></div>
</div>



<p>And that’s it! The handler method is the one we discussed a bit earlier, where the items in the itemsource are converted into Children elements.</p>

<p>Here’s how to use it in XAML</p>

<div id="codeSnippetWrapper" style="font-size: 8pt; overflow: auto; cursor: text; border-top: silver 1px solid; font-family: &#39;Courier New&#39;, courier, monospace; border-right: silver 1px solid; width: 97.5%; border-bottom: silver 1px solid; padding-bottom: 4px; direction: ltr; text-align: left; padding-top: 4px; padding-left: 4px; border-left: silver 1px solid; margin: 20px 0px 10px; line-height: 12pt; padding-right: 4px; max-height: 200px; background-color: #f4f4f4">
  <div id="codeSnippet" style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4">
    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum1" style="color: #606060">   1:</span> <span style="color: #0000ff">&lt;</span><span style="color: #800000">controls:AwesomeWrappanel</span> <span style="color: #ff0000">ItemsSource</span><span style="color: #0000ff">=&quot;{Binding Persons}&quot;</span> <span style="color: #ff0000">Orientation</span><span style="color: #0000ff">=&quot;Horizontal&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum2" style="color: #606060">   2:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">controls:AwesomeWrappanel.ItemTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum3" style="color: #606060">   3:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">DataTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum4" style="color: #606060">   4:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">StackLayout</span> <span style="color: #ff0000">BackgroundColor</span><span style="color: #0000ff">=&quot;Red&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum5" style="color: #606060">   5:</span>                 <span style="color: #0000ff">&lt;</span><span style="color: #800000">Label</span> <span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;{Binding Name}&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum6" style="color: #606060">   6:</span>                 <span style="color: #0000ff">&lt;</span><span style="color: #800000">Label</span> <span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;{Binding Age}&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum7" style="color: #606060">   7:</span>             <span style="color: #0000ff">&lt;/</span><span style="color: #800000">StackLayout</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum8" style="color: #606060">   8:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">DataTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: white"><span id="lnum9" style="color: #606060">   9:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">controls:AwesomeWrappanel.ItemTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-top-style: none; font-size: 8pt; overflow: visible; border-left-style: none; font-family: &#39;Courier New&#39;, courier, monospace; width: 100%; border-bottom-style: none; color: black; padding-bottom: 0px; direction: ltr; text-align: left; padding-top: 0px; border-right-style: none; padding-left: 0px; margin: 0em; line-height: 12pt; padding-right: 0px; background-color: #f4f4f4"><span id="lnum10" style="color: #606060">  10:</span> <span style="color: #0000ff">&lt;/</span><span style="color: #800000">controls:AwesomeWrappanel</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF--></div>
</div>

<p>A quick screenshot of the result:</p>

<p><a href="http://i58.tinypic.com/2coiy5h.jpg" target="_blank"><img src="http://i58.tinypic.com/2coiy5h.jpg" width="77" height="128" /></a></p>

<p>A demo project can be found on my <a href="http://1drv.ms/19NfmAJ" target="_blank">OneDrive</a> or see this <a href="https://gist.github.com/NicoVermeir/7ffb34ebd639ed958382" target="_blank">GitHub Gist</a> for the Wrappanel class.</p>

<p>Happy coding!</p>
{% include imported_disclaimer.html %}
