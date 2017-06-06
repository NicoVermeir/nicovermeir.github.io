---
layout: post
title: "filtering collections from xaml using collectionviewsource"
date: 2012-04-12 17:18:01 +0200
comments: true
published: true
categories: ["post"]
tags: [".Net", "Binding", "MVVM Light", "Windows 8", "XAML", "WP7", "WP8", "Silverlight", "Metro", "Devices"]
alias: ["/blog/post/2012/04/12/Filtering-collections-from-XAML-using-CollectionViewSource.aspx", "/blog/post/2012/04/12/filtering-collections-from-xaml-using-collectionviewsource.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2012/04/12/Filtering-collections-from-XAML-using-CollectionViewSource.aspx
 - /blog/post/2012/04/12/filtering-collections-from-xaml-using-collectionviewsource.aspx
---
<p>I find that I often run into the need of separating a collection of items into several collections just so I can bind them to multiple listboxes, for example a list of sessions spanning several tracks and each track is shown in his own listbox in a pivotitem. To get this done you can start by adding multiple collections to your viewmodel and divide the items there. However this makes your viewmodel very big in a very short time. A better way to do this is using CollectionViewSource items in XAML. Let me show you how.</p>  <p>First thing I did was building a demo class existing out of a title and a description, these two properties will be shown in the listbox later on. A third property is the one we’ll use to filter the data, here’s the completed class.</p>  <pre class="code"><span style="color: blue">public class </span><span style="color: #2b91af">DemoClass
</span>{
    <span style="color: blue">public string </span>Title { <span style="color: blue">get</span>; <span style="color: blue">set</span>; }
    <span style="color: blue">public string </span>Description { <span style="color: blue">get</span>; <span style="color: blue">set</span>; }
    <span style="color: blue">public </span><span style="color: #2b91af">Pivot </span>PivotToAppearIn { <span style="color: blue">get</span>; <span style="color: blue">set</span>; }
}</pre>

<p>Nothing special here. Notice the Pivot instance, this is just an Enum that will be the way to filter later on.</p>

<pre class="code"><span style="color: blue">public enum </span><span style="color: #2b91af">Pivot
</span>{
    First,
    Second,
    All
}</pre>

<p>For the demo’s purpose I’ll be creating a bunch of dummy data in the viewmodel. The project template I’ve used here is the default pivot app template in the Windows Phone 7.1.1 SDK. It comes with a bunch of dummy data, I’ve used the same data but put them in instances of the DemoClass. Those instances are put inside an ObservableCollection.</p>

<p>This is the viewmodel</p>

<pre class="code"><span style="color: blue">public class </span><span style="color: #2b91af">MainViewModel </span>: <span style="color: #2b91af">INotifyPropertyChanged
</span>{
    <span style="color: blue">public const string </span>ItemsPropertyName = <span style="color: #a31515">&quot;Items&quot;</span>;
    <span style="color: blue">private </span><span style="color: #2b91af">ObservableCollection</span>&lt;<span style="color: #2b91af">DemoClass</span>&gt; items;
    <span style="color: blue">public </span><span style="color: #2b91af">ObservableCollection</span>&lt;<span style="color: #2b91af">DemoClass</span>&gt; Items
    {
        <span style="color: blue">get
        </span>{
            <span style="color: blue">return </span>items;
        }

        <span style="color: blue">set
        </span>{
            <span style="color: blue">if </span>(items == <span style="color: blue">value</span>)
            {
                <span style="color: blue">return</span>;
            }

            items = <span style="color: blue">value</span>;
            NotifyPropertyChanged(ItemsPropertyName);
        }
    }

    <span style="color: blue">public </span>MainViewModel()
    {
        <span style="color: blue">this</span>.Items = <span style="color: blue">new </span><span style="color: #2b91af">ObservableCollection</span>&lt;<span style="color: #2b91af">DemoClass</span>&gt;();
        LoadData();
    }

    <span style="color: blue">public void </span>LoadData()
    {
        Items.Add(<span style="color: blue">new </span><span style="color: #2b91af">DemoClass 
        </span>{ 
            Title = <span style="color: #a31515">&quot;runtime one&quot;</span>, 
            Description = <span style="color: #a31515">&quot;Maecenas praesent accumsan bibendum&quot;</span>, 
            PivotToAppearIn = <span style="color: #2b91af">Pivot</span>.First 
        });
    }

    <span style="color: blue">public event </span><span style="color: #2b91af">PropertyChangedEventHandler </span>PropertyChanged;
    <span style="color: blue">private void </span>NotifyPropertyChanged(<span style="color: #2b91af">String </span>propertyName)
    {
        <span style="color: #2b91af">PropertyChangedEventHandler </span>handler = PropertyChanged;
        <span style="color: blue">if </span>(<span style="color: blue">null </span>!= handler)
        {
            handler(<span style="color: blue">this</span>, <span style="color: blue">new </span><span style="color: #2b91af">PropertyChangedEventArgs</span>(propertyName));
        }
    }
}</pre>

<p>So we’ve got our basic bindable property here and a method that loads in the dummy data. In the demo project there’s obviously more then one item in the collection, there’s about 16 to be precise.</p>

<p>In the design I didn’t change a lot from the default template. I’ve just copied the ItemTemplate from the listbox to the pageresources so that it can be reused in the second listbox.</p>

<p>This is the template.</p>

<pre class="code"><span style="color: blue">&lt;</span><span style="color: #a31515">phone</span><span style="color: blue">:</span><span style="color: #a31515">PhoneApplicationPage.Resources</span><span style="color: blue">&gt;
    </span><span style="color: green">&lt;!-- template for the listboxes --&gt;
    </span><span style="color: blue">&lt;</span><span style="color: #a31515">DataTemplate </span><span style="color: red">x</span><span style="color: blue">:</span><span style="color: red">Name</span><span style="color: blue">=&quot;ListBoxTemplate&quot;&gt;
            &lt;</span><span style="color: #a31515">StackPanel </span><span style="color: red">Margin</span><span style="color: blue">=&quot;0,0,0,17&quot; </span><span style="color: red">Width</span><span style="color: blue">=&quot;432&quot; </span><span style="color: red">Height</span><span style="color: blue">=&quot;78&quot;&gt;
                &lt;</span><span style="color: #a31515">TextBlock </span><span style="color: red">Text</span><span style="color: blue">=&quot;{</span><span style="color: #a31515">Binding </span><span style="color: red">Title</span><span style="color: blue">}&quot; </span><span style="color: red">TextWrapping</span><span style="color: blue">=&quot;Wrap&quot; 
                           </span><span style="color: red">Style</span><span style="color: blue">=&quot;{</span><span style="color: #a31515">StaticResource </span><span style="color: red">PhoneTextExtraLargeStyle</span><span style="color: blue">}&quot;/&gt;
                &lt;</span><span style="color: #a31515">TextBlock </span><span style="color: red">Text</span><span style="color: blue">=&quot;{</span><span style="color: #a31515">Binding </span><span style="color: red">Description</span><span style="color: blue">}&quot; </span><span style="color: red">TextWrapping</span><span style="color: blue">=&quot;Wrap&quot; 
                           </span><span style="color: red">Margin</span><span style="color: blue">=&quot;12,-6,12,0&quot; </span><span style="color: red">Style</span><span style="color: blue">=&quot;{</span><span style="color: #a31515">StaticResource </span><span style="color: red">PhoneTextSubtleStyle</span><span style="color: blue">}&quot;/&gt;
            &lt;/</span><span style="color: #a31515">StackPanel</span><span style="color: blue">&gt;
        &lt;/</span><span style="color: #a31515">DataTemplate</span><span style="color: blue">&gt;
&lt;/</span><span style="color: #a31515">phone</span><span style="color: blue">:</span><span style="color: #a31515">PhoneApplicationPage.Resources</span><span style="color: blue">&gt;
</span></pre>

<p>All right now that the preparations are set, time to get into the filtering. First step is to add a CollectionViewSource for each listbox. These are set on the same place as I’ve put the listbox ItemTemplate, in the pageresources. For this demo I need two CollectionViewSources.</p>

<pre class="code"><span style="color: blue">&lt;</span><span style="color: #a31515">CollectionViewSource </span><span style="color: red">x</span><span style="color: blue">:</span><span style="color: red">Name</span><span style="color: blue">=&quot;FirstPivot&quot; </span><span style="color: red">Filter</span><span style="color: blue">=&quot;FirstPivot_Filter&quot; </span><span style="color: red">Source</span><span style="color: blue">=&quot;{</span><span style="color: #a31515">Binding </span><span style="color: red">Items</span><span style="color: blue">}&quot; /&gt;
&lt;</span><span style="color: #a31515">CollectionViewSource </span><span style="color: red">x</span><span style="color: blue">:</span><span style="color: red">Name</span><span style="color: blue">=&quot;SecondPivot&quot; </span><span style="color: red">Filter</span><span style="color: blue">=&quot;SecondPivot_Filter&quot; </span><span style="color: red">Source</span><span style="color: blue">=&quot;{</span><span style="color: #a31515">Binding </span><span style="color: red">Items</span><span style="color: blue">}&quot; /&gt;
</span></pre>

<p>So what’s all this? x:Name is like in any other XAML object, it’s just the name that can be used to reference the object. The source is the ObservableCollection that was created in the viewmodel. And last but definitely not least is the Filter event. This event will fire for every item in the collection that is bound to the Source property.</p>

<p>Now for the event handler, I’ll just post the event handler FirstPivot_Filter here because they are basically the same.</p>

<pre class="code"><span style="color: blue">private void </span>FirstPivot_Filter(<span style="color: blue">object </span>sender, System.Windows.Data.<span style="color: #2b91af">FilterEventArgs </span>e)
{
    e.Accepted = (e.Item <span style="color: blue">as </span><span style="color: #2b91af">DemoClass</span>).PivotToAppearIn == Model.<span style="color: #2b91af">Pivot</span>.First || 
        (e.Item <span style="color: blue">as </span><span style="color: #2b91af">DemoClass</span>).PivotToAppearIn == Model.<span style="color: #2b91af">Pivot</span>.All;
}</pre>

<p>FilterEventArgs has two properties, Accepted is a boolean that when true shows the item in the listbox that is bound to the CollectionViewSource. Item is the current item in the collection. Remember that this event is triggered for each item in the collection. So what we do here is casting the Item property to an instance of DemoClass then check if the PivotToAppearIn property, that was an instance of the enum, is either First or All.</p>

<p>Now that we have the CollectionViewSources and the event handlers in place it’s time to bind the ViewSource to the listbox.</p>

<pre class="code"><span style="color: blue">&lt;</span><span style="color: #a31515">controls</span><span style="color: blue">:</span><span style="color: #a31515">PivotItem </span><span style="color: red">Header</span><span style="color: blue">=&quot;first&quot;&gt;
    &lt;</span><span style="color: #a31515">ListBox </span><span style="color: red">x</span><span style="color: blue">:</span><span style="color: red">Name</span><span style="color: blue">=&quot;FirstListBox&quot; </span><span style="color: red">Margin</span><span style="color: blue">=&quot;0,0,-12,0&quot; 
             </span><span style="color: red">ItemsSource</span><span style="color: blue">=&quot;{</span><span style="color: #a31515">Binding </span><span style="color: red">Source</span><span style="color: blue">={</span><span style="color: #a31515">StaticResource </span><span style="color: red">FirstPivot</span><span style="color: blue">}}&quot;
             </span><span style="color: red">ItemTemplate</span><span style="color: blue">=&quot;{</span><span style="color: #a31515">StaticResource </span><span style="color: red">ListBoxTemplate</span><span style="color: blue">}&quot; /&gt;
&lt;/</span><span style="color: #a31515">controls</span><span style="color: blue">:</span><span style="color: #a31515">PivotItem</span><span style="color: blue">&gt;
</span></pre>

<p>The bindingsource of ItemsSource is bound to the CollectionViewSource that filters for this listbox. And that’s it!</p>

<p>&#160;</p>

<p>In this article I’ve shown how you can filter a collection using a CollectionViewSource in XAML. This is an easy and fast way to visually filter data while keeping a clean ViewModel.</p>

<p>Download the Demo project <a href="https://skydrive.live.com/redir.aspx?cid=5a345bb15e973473&amp;resid=5A345BB15E973473!250&amp;parid=root" target="_blank">here</a>.</p>
{% include imported_disclaimer.html %}
