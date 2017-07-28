---
layout: post
title: Binding to the Window Title in UWP
date: 2017-07-28
comments: true
published: true
categories: ["post"]
tags: [".Net", "UWP", "Binding", "XAML"]
author: Nico Vermeir
---
I was recently in a scenario where I had a UWP app with a timer. The running timer was supposed to be displayed in the title bar of the app (the app is a UWP app that will run only on laptops). To my surprise, this wasn't possible out of the box. We can set a title but we can't bind to it, since the title property cannot be accessed in XAML.

### The default way to set the title

Setting the title is very much the same workflow as setting the title bar's color, or the buttons in the title bar.

```csharp
var window = ApplicationView.GetForCurrentView();
window.Title = "Hello World!";
```

When you run this, you'll notice that the window's title bar says "Hello World - AppName". No matter what you set the title as, the system will always append the appname to it.

Now, how to get this into a binding? The answer is through an attached property.

### Attached Properties

So, what are attached properties? Attached properties are a form of global properties that can be set on a XAML element. An example of attached properties that you have no doubt used are *Grid.Row* and *Grid.Column*. Custom attached properties are often used when you want to expose a property in XAML. Exactly what we want to do here.

### Creating the Attached Property

To create an attached property you'll need a static class and a special form of Dependency Property. Instead of calling *DependencyProperty.Register*, we need to call *DependencyProperty.RegisterAttached*. Another difference is the underlying property. Instead of an actual property we need to write two methods, something that will look familiar to those that know a bit of Java. The naming of those methods are important since Attached Properties are convention based.

Here's the full example

```csharp
public static class PageExtensions
{
    public static readonly DependencyProperty TitleProperty =
        DependencyProperty.RegisterAttached("Title", typeof(string),
            typeof(PageExtensions),
            new PropertyMetadata(string.Empty, OnTitlePropertyChanged));

    public static string GetTitle(DependencyObject d)
    {   
        return d.GetValue(TitleProperty).ToString();
    }

    public static void SetTitle(DependencyObject d, string value)
    {
        d.SetValue(TitleProperty, value);
    }

    private static void OnTitlePropertyChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
    {
        string title = e.NewValue.ToString();
        var window= ApplicationView.GetForCurrentView();
        window.Title = title;
    }
}
```

The actual logic of setting the window title happens in the property changed event handler.

### Binding to the property

Final step, time to see if this actually works!

I've added a string property in my MainPage.xaml.cs.

```csharp
public sealed partial class MainPage : Page
{
    public string WindowTitle { get; set; }

    public MainPage()
    {
        InitializeComponent();

        WindowTitle = "Hello World!";
    }
}
```

Very simple example here, but enough to show the gist of it. Next, in MainPage.xaml we can add the binding (extra lines omitted for clarity)

```xml
<Page x:Class="App10.MainPage"
      ...
      xmlns:local="using:App10"
      local:PageExtensions.Title="{x:Bind WindowTitle}"
      mc:Ignorable="d">
```

Run the app, and the value of the string property should be set as the window title! (Note: I used x:Bind here, so no need to set the DataContext, it's always the code-behind)

### Conclusion

In this article I've shown how I tackled a problem that I ran into this week, binding a viewmodel property to the window title in a UWP app. In the real application I've used a TimeSpan to bind to the window title, it goes up every second and the title is refreshed every second as well. 

Happy coding!