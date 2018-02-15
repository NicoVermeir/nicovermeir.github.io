---
layout: post
title: Migrating to the MVVM Light .NET Standard version
date: 2018-02-14
comments: true
published: true
categories: ["post"]
tags: [".Net", "UWP", "Binding", "XAML", "Xamarin"]
author: Nico Vermeir
---
Last year I blogged about <a href="http://www.spikie.be/post/2017/06/23/building-a-netstandard-xamarin-app-with-mvvm-light.html" target="_blank">enabling PCL compatibility in .NET Standard</a> libraries so we could use libraries that weren't updated to .NET Standard yet. In that post I've used MVVM Light as an example. Recently Laurent Bugnion announced the <a href="http://blog.galasoft.ch/posts/2018/02/publishing-mvvmlight-v5-4-1-with-net-standard-support/" target="_blank">release of MVVM Light 5.4.1</a>, with .NET Standard support. Time to dust of the old sample and try to upgrade it!

### The app
The app we're upgrading in this post is the exact same one I've used for the post last year. So we have a Xamarin Android app with a .NET Standard library as core project. The library contains the models and viewmodels.

<img src="http://www.spikie.be/images/netstandardmvvm/03.png" alt="screenshot" style="width: 250px;"/>


### Getting the bits
First step is updating the NuGet packages. When opening the NuGet package manager we immediately see version 5.4.1 of MVVM Light between the available updates. However, this version is still PCL based and we want the .NET Standard version. So instead of updating these versions, we'll just uninstall them. I've removed MVVMLightLibs, CommonServiceLocator and the PCL Compatibility pack.

<img src="http://www.spikie.be/images/updatingMvvmLight/01.PNG" alt="screenshot" style="width: 250px;"/>

The .NET Standard version is called MvvmLightLibsStd10 and can be found on NuGet as a stable release.

<img src="http://www.spikie.be/images/updatingMvvmLight/02.PNG" alt="screenshot" style="width: 250px;"/>

Once the NuGet package is installed, you'll notice that the ViewModelLocator is having some trouble. The **Microsoft.Practices.ServiceLocation** namespace cannot be resolved. The main reason here is that <a href="http://www.mvvmlight.net/std10/" target="_blank">MVVM Light has dropped its dependency on the CommonServiceLocator package</a>.

To fix this, we need to replace all references to ServiceLocator with calls to SimpleIoc (or any other IOC container that you might prefer).

```csharp
using GalaSoft.MvvmLight.Ioc;

namespace NetStandardDemo.Core.ViewModel
{
    public class ViewModelLocator
    {
        public ViewModelLocator()
        {
            SimpleIoc.Default.Register<MainViewModel>();
        }

        public MainViewModel Main => SimpleIoc.Default.GetInstance<MainViewModel>();
    }
}
```

Now, at this point I tried running the app again but got a bunch of weird errors, first some TargetInvocationExceptions, then something about tokens. I cleaned / rebuild the solution and removed the app from the Android device to get everything up and running again.

### Cleaning up the csproj
Since, in this demo app's case, we no longer have any dependencies on a PCL, we can safely remove the fallback mechanism that was installed in the <a href="http://www.spikie.be/post/2017/06/23/building-a-netstandard-xamarin-app-with-mvvm-light.html" target="_blank">previous post</a>.

The project file for the .NET Standard shared library looks like this

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netstandard1.6</TargetFramework>
    <PackageTargetFallback>portable-net45+win8+wpa81+wp8</PackageTargetFallback>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="CommonServiceLocator" Version="2.0.2" />
    <PackageReference Include="MvvmLightLibsStd10" Version="5.4.1" />
    <PackageReference Include="System.ComponentModel" Version="4.3.0" />
  </ItemGroup>

</Project>
```

The **PackageTargetFallback** tag can be completely removed **IF you're very certain that you no longer have any PCL references left!**

This is the cleaned up version of the csproj.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netstandard1.6</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="MvvmLightLibsStd10" Version="5.4.1" />
    <PackageReference Include="System.ComponentModel" Version="4.3.0" />
  </ItemGroup>

</Project>
```

Unload / Reload the project in Visual Studio to have the changes take effect.


### But what about the ServiceLocator pattern?
You can still install the CommonServiceLocator pattern, but it doesn't work with SimpleIoc anymore. I've reached out to Laurent about this, he replied:

> My recommendation with. Net std would be to avoid the commonservicelocator altogether. I had some discussions with the .net team and came to see the CSL as an antipattern. Yes the idea behind it is not bad but honestly how often are you swapping service locators? The cost of the dependency is quite high (like the switch to .net standard has shown...) and the benefits are small. 
> 
> My recommendation is to just use Simple IOC in the code and to take the hit in case you change service Locator. 
> 


I agree with this. As Laurent mentions, it's an extra abstraction layer who's one purpose is to save some time when you should decide to swap containers during the development of the applications, and how often does that happen? So when it does happen, just change all the container stuff instead of adding an extra abstraction layer.

### Fine, so your little demo works, I bet in a real app it's not so easy!
Actually, it is! The idea for this post came from migrating an actual Xamarin Forms app to the latest MVVM Light bits. The procedure was exactly the same, remove the MVVM Light packages, install the .NET Standard versions, clean / rebuild because of an error, remove the ServiceLocator calls, and everything worked!

### Wrapping up
In this post I've shown how easy it is to switch to the .NET Standard version of MVVM Light, in a scenario where I was already using MVVM Light in a .NET Standard library by making use of **PackageTargetFallback**.

Should you make the effort to do this? It depends I guess. How far along is your application? Are you making use of ServiceLocator all over the place? Looking at it from a technical debt perspective, I would advise to upgrade. PCL is on its way out, .NET Standard is the way forward.

Happy coding!