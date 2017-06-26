---
layout: post
title:  "Building a netstandard Xamarin app with MVVM Light"
date:   2017-06-23
comments: true
published: true
categories: ["post"]
tags: ["xamarin", "mvvm-light", "netstandard"]
author: "Nico Vermeir"
---
### The problem
Netstandard libraries are meant to replace Portable Class Libraries. And they do sound pretty good. The only problem right now is that we're in a transition phase. A bunch of NuGet packages are not updated to be netstandard compatible yet. But that can't stop us!

### The solution
The solution is basically a matter of configuring your netstandard libraries to be compatible with a certain PCL profile. But let's not get ahead of ourselves and start from the beginning.

### Project setup
For this post we'll start from an empty solution. To create an empty solution in Visual Studio 2017 go to File > New Project. In the new project dialog, select Other Project Types > Visual Studio Solutions. That should show you the Empty Solution project template

![](http:\\www.spikie.be\images\netstandardmvvm\01.PNG)

Next, right-click the empty solution > Add > New Project and select the Class Library (.NET Standard) project template. This will be our shared, or core, project.

![](http:\\www.spikie.be\images\netstandardmvvm\02.PNG)

By default, this will be a netstandard 1.4 project. This can easily be switched to another version from the project properties. The version you should use depends on the platforms you want to target. See <a href="https://docs.microsoft.com/en-us/dotnet/standard/library" target="_blank">this link</a> to easily choose the right version.

Now, if we would try to add the MVVM Light NuGet package we would get an error like

```txt
Package MvvmLightLibs 5.3.0 is not compatible with netstandard1.4 (.NETStandard,Version=v1.4). Package MvvmLightLibs 5.3.0 supports:
  - monoandroid10 (MonoAndroid,Version=v1.0)
  - net35 (.NETFramework,Version=v3.5)
  - net40 (.NETFramework,Version=v4.0)
  - net45 (.NETFramework,Version=v4.5)
  - portable-net45+win8+wp8+wpa81 (.NETPortable,Version=v0.0,Profile=Profile259)
  - sl5 (Silverlight,Version=v5.0)
  - win81 (Windows,Version=v8.1)
  - wp8 (WindowsPhone,Version=v8.0)
  - wp81 (WindowsPhone,Version=v8.1)
  - wpa81 (WindowsPhoneApp,Version=v8.1)
  - xamarinios10 (Xamarin.iOS,Version=v1.0)
One or more packages are incompatible with .NETStandard,Version=v1.4.
Package restore failed. Rolling back package changes for 'NetStandardDemo.Core'.
```

The solution for this came to me in the form of <a href="https://oren.codes/2017/04/23/using-xamarin-forms-with-net-standard-vs-2017-edition/" target="_blank">Oren and one of his amazing blog posts.</a> .NET Standard projects can fall back to PCL profiles when needed! This cannot be done from the UI, so we'll need to edit the XML behind the .NET Standard project. Luckily, Visual Studio 2017 allows us to edit the csproj without having to unload the project first.

Right-click the netstandard project and select Edit \<projectname\>.csproj

Add the following line in the ProjectGroup tag

```xml
<PackageTargetFallback>portable-net45+win8+wpa81+wp8</PackageTargetFallback>
```

The complete csproj looks like this

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard1.4</TargetFramework>
    <PackageTargetFallback>portable-net45+win8+wpa81+wp8</PackageTargetFallback>
  </PropertyGroup>
</Project>
```

If we try to add MVVM Light again, we get a whole other message

```txt
Installing NuGet package MvvmLightLibs 5.3.0.
Committing restore...
Writing lock file to disk. Path: D:\Projects\NetStandardDemo\NetStandardDemo.Core\obj\project.assets.json
Restore completed in 581.25 ms for D:\Projects\NetStandardDemo\NetStandardDemo.Core\NetStandardDemo.Core.csproj.
Successfully installed 'CommonServiceLocator 1.3.0' to NetStandardDemo.Core
Successfully installed 'MvvmLightLibs 5.3.0' to NetStandardDemo.Core
Executing nuget actions took 1.89 sec
```

Adding a ViewModel that derives from ViewModelBase works like a charm

```csharp
public class MainViewModel : ViewModelBase
{
}
```

However, the ViewModelLocator is something else. We get compile errors when adding this

```csharp
public class ViewModelLocator
{
    public ViewModelLocator()
    {
        ServiceLocator.SetLocatorProvider(() => SimpleIoc.Default);
        
        SimpleIoc.Default.Register<MainViewModel>();
    }

    public MainViewModel Main => ServiceLocator.Current.GetInstance<MainViewModel>();
}
```

This generates the following error

```txt
The type 'IServiceProvider' is defined in an assembly that is not referenced. You must add a reference to assembly 'System.ComponentModel, Version=0.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a'.
```

The problem here is that MVVM Light makes use of <a href="https://msdn.microsoft.com/en-us/library/ff648968.aspx" target="_blank">the ServiceLocator pattern</a> via the <a href="https://www.nuget.org/packages/CommonServiceLocator/" target="_blank">CommonServiceLocator package</a>. The SetLocatorProvider expects an object that implements IServiceProvider, and that interface lives in System.ComponentModel.

All we need to do to fix this is add the <a href="https://www.nuget.org/packages/System.ComponentModel/" target="_blank">System.ComponentModel NuGet package</a> to the project and we're good to go!

### Using the ViewModels
Now, let's see if we can actually start using the viewmodel and the .NET Standard project. Right-click the solution and add a Xamarin platform as target. I'm using Xamarin Android for this article.

The Xamarin Android template includes one page, Main.axml. That should suffice for this demo. I've added a TextView to the page. The xml looks like this.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Text"
        android:layout_height="wrap_content"
        android:id="@+id/textViewGreeting"
        android:textSize="30dp"
        android:layout_gravity="center"
        android:layout_width="wrap_content" />
</LinearLayout>
```

In the MainViewModel I've added a read only string property

```csharp
using GalaSoft.MvvmLight;

namespace NetStandardDemo.Core.ViewModel
{
    public class MainViewModel : ViewModelBase
    {
        public string Greeting => "Hello from .NET Standard!";
    }
}
```

All that's left to do is hook them up to each other. Start by referencing the Core project from the Android project. Next, we'll need to add the <a href="https://www.nuget.org/packages/MvvmLightLibs/" target="_blank">MVVM Light libs</a> project from NuGet to the Android project so that we can use the included binding framework.

The MainActivity.cs in the Android project is where all the magic will happen. Start by adding some fields and a property for the ViewModel.

```csharp
private List<Binding> _bindings;
private TextView _textView;

public MainViewModel Vm => App.Locator.Main;
```

As you probably can tell, we don't have anything called App just yet. The App class is where, in XAML based applications, the app starts. It holds a reference to the ViewModelLocator that is used in every binding statement. Since we're using Xamarin Android and not Xamarin Forms we need some way to get to the ViewModelLocator, this is where the App class comes in. Add a new class to the Core project.

```csharp
public static class App
{
    private static ViewModelLocator _locator;
    public static ViewModelLocator Locator => _locator ?? (_locator = new ViewModelLocator());
}
```

Back in the MainActivity there should be an OnCreate method already there. This is the method that is executed when Android launches this activity (think of an activity as the code behind of a page in Android).

We need to do a few things in this method. First, we instantiate the list of bindings, next we will set the view of this activity to Main.axml, then we need to get a reference to the textview in Main.axml and finally we need to create the binding from the ViewModel's Greeting property to the textview's Text property.

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    _bindings = new List<Binding>();

    SetContentView (Resource.Layout.Main);

    _textView = FindViewById<TextView>(Resource.Id.textViewGreeting);
    var binding = this.SetBinding(() => Vm.Greeting, () => _textView.Text);
    _bindings.Add(binding);
}
```

SetBinding is an extension method in MVVM Light used to create bindings. The overload we're using here takes two Func parameters, the source of the binding and the target. The source is the property on the viewmodel, the target is the property of the element. Before we can use the textview in code, we need to have a reference to it. In Android this is done by searching for it by ID in the visual tree. The Activity base class contains the FindViewById method for this.

Finally, we add the newly created binding to the list of bindings. This is done so that the garbage collector won't accidentally clean up the binding while we still need it.

That should be enough to get things going, the result should look like this:
<img src="http://www.spikie.be/images/netstandardmvvm/03.png" alt="screenshot" style="width: 250px;"/>

### Conclusion
In this article I've shown you how you can use MVVM Light on .NET Standard based projects. This will also work for other libraries that have not yet made the jump to .NET Standard.

Happy coding!