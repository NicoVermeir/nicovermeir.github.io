---
layout: post
title: Animating out of an extended splashscreen
date: 2018-03-09
comments: true
published: true
categories: ["post"]
tags: [".Net", "UWP", "Composition", "XAML", "Connected Animation"]
author: Nico Vermeir
---
Animations are a key part of making your app feel fluent, modern and responsive. Connected animations are a very big part of this, and have been around for a while now. With connected animations you seemingly connect a list item with a detail page for example.

***Note: This post and demo are created with Fall Creators Update***

![Connected animation mockup](https://docs.microsoft.com/en-us/windows/uwp/design/motion/images/connected-animations/example.gif)

Connected animations have been made quite easy to do in UWP apps, but I hardly ever see them used on app launch. This is quite surprising as this is the very first interaction a user has with your app. Why not animating your splashscreen into your mainpage?

<img src="http://www.spikie.be/images/connectedanimation/01.gif" alt="screenshot" style="width: 500px;"/>

Let's split this up in two parts, we'll start with the extended splashscreen and end with the connected animation.

### Adding an extended splashscreen
Every UWP app has a splashscreen. Splashscreens are shown while the app is initialising, however, if the app takes longer to start it might feel like it's stuck. This is where an extended splashscreen comes in.

Out of the box we can only choose a background color and image to show on the splashscreen, through the use of the <a href="https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Activation.SplashScreen" target="_blank">SplashScreen</a> class we can hook into the splashscreen and show our own custom version.

An extended splashscreen is nothing more than a `Page` we've added to the app, very similar to the MainPage for example. So, let's add a new page and a few private fields.

```csharp
partial class ExtendedSplash : Page
{
    private readonly Frame _rootFrame;
    private readonly SplashScreen _splash; 
    private Rect _splashImageRect;
    
    public ExtendedSplash()
    {
        InitializeComponent();
    }
}
```

`Page.Frame` property is not available yet at this point of app startup, so we will hold a reference to the rootframe in a private field. The SplashScreen field holds a reference to the default splashscreen, the Rect will hold the position and size of the default splashscreen image.


Now, for the XAML

```xml
<Page x:Class="AnimatedSplash.ExtendedSplash"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
      xmlns:local="using:AnimatedSplash"
      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
      mc:Ignorable="d">

    <Grid Background="White">
        <Canvas>
            <Image x:Name="ExtendedSplashImage" Source="Assets/SplashScreen.png" />
            <ProgressRing Name="SplashProgressRing"
                          Width="20"
                          HorizontalAlignment="Center"
                          IsActive="True" />
        </Canvas>
    </Grid>
</Page>
```

SplashScreen.png is an image I've added to the assets folder and set as the splashscreen image. In the extended splashscreen it lives within a `Canvas` element. The reason for this is because we'll need to position the `Image` and the `ProgressRing` on the exact same position as on the normal splashscreen, we need to do this from code since the position can change at any time by a user resizing the app window.

Now we have everything we need to position the `Image` and the `ProgressRing` exactly where we need them.

```csharp
private void PositionImage()
{
    Canvas.SetLeft(ExtendedSplashImage, _splashImageRect.X);
    Canvas.SetTop(ExtendedSplashImage, _splashImageRect.Y);
            
    ExtendedSplashImage.Height = _splashImageRect.Height;
    ExtendedSplashImage.Width = _splashImageRect.Width;
}

private void PositionRing()
{
    double leftValue = _splashImageRect.X + (_splashImageRect.Width * 0.5) - (SplashProgressRing.Width * 0.5);
    double topValue = (_splashImageRect.Y + _splashImageRect.Height + _splashImageRect.Height * 0.1);

    Canvas.SetLeft(SplashProgressRing, leftValue);
    Canvas.SetTop(SplashProgressRing, topValue);
}
```

We'll use the `Canvas` attached properties to set the image's left and top coordinates, and its width and height, to the same as the image on the default splashscreen. I'd like the `ProgressRing` to be centered underneath the image. We can calculate that position by taking 50% of the width of the image, substracting 50% of the width of the progress ring and adding the X coordinate of the image. By using the same formula with the Y coordinate we also have the top property.

To simulate the app being busy with loading stuff we add a `Task.Delay`

```csharp
private async Task SetupApp()
{
    await Task.Delay(2000);
}
```

This obviously needs to be replaced with real loading code :)

In the end, when the loading is completed, we'll need a way to move from the extended splash to the `MainPage`. We have a field holding what will be the rootframe, so we can use that to navigate away.

```csharp
private void DismissExtendedSplash()
{
    _rootFrame.Navigate(typeof(MainPage));
}
```

Now for the constructor

```csharp
public ExtendedSplash(SplashScreen splashscreen)
{
    InitializeComponent();

    _rootFrame = Window.Current.Content as Frame;
    _splash = splashscreen;

    Window.Current.SizeChanged += ExtendedSplash_OnResize;

    if (_splash != null)
    {
        _splash.Dismissed += DismissedEventHandler;
        _splashImageRect = _splash.ImageLocation;
        
        PositionImage();
        PositionRing();
    }
}
```

The constructor takes in a `SplashScreen` parameter that we set to the private field we declared earlier. The Frame field will be set to `Window.Current.Content`. We'll need an event handler for when the app is resized, so we can recenter the `Image`, and for when the default splashscreen is dismissed. The position of the image on the splashscreen is set to the Rect we declared earlier.

```csharp
private void ExtendedSplash_OnResize(Object sender, WindowSizeChangedEventArgs e)
{
    if (_splash != null)
    {
        _splashImageRect = _splash.ImageLocation;
        PositionImage();
        PositionRing();
    }
}
```

Whenever the size of the page changes, we get the new imagelocation from the splashscreen and recalculate the position of the elements.

When the default splashscreen is dismissed we can trigger our custom loading code, once that is finished we can dismiss the extended splash screen. Keep in mind that this needs to happen on the UI thread, so we'll need to marshall it through the `Dispatcher`.

```csharp
private async void DismissedEventHandler(SplashScreen sender, object e)
{
    await SetupApp();
    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, DismissExtendedSplash);
}
```

Here's the complete `ExtendedSplashPage`.

```csharp
partial class ExtendedSplash : Page
{
    private readonly Frame _rootFrame;
    private readonly SplashScreen _splash; 
    private Rect _splashImageRect;

    public ExtendedSplash(SplashScreen splashscreen)
    {
        InitializeComponent();
        
        _rootFrame = Window.Current.Content as Frame;
        _splash = splashscreen;

        Window.Current.SizeChanged += ExtendedSplash_OnResize;

        if (_splash != null)
        {
            _splash.Dismissed += DismissedEventHandler;
            _splashImageRect = _splash.ImageLocation;

            PositionImage();
            PositionRing();
        }
    }
    
    private void PositionImage()
    {
        Canvas.SetLeft(ExtendedSplashImage, _splashImageRect.X);
        Canvas.SetTop(ExtendedSplashImage, _splashImageRect.Y);
        
        ExtendedSplashImage.Height = _splashImageRect.Height;
        ExtendedSplashImage.Width = _splashImageRect.Width;
    }

    private void PositionRing()
    {
        double leftValue = _splashImageRect.X + (_splashImageRect.Width * 0.5) - (SplashProgressRing.Width * 0.5);
        double topValue = (_splashImageRect.Y + _splashImageRect.Height + _splashImageRect.Height * 0.1);

        Canvas.SetLeft(SplashProgressRing, leftValue);
        Canvas.SetTop(SplashProgressRing, topValue);
    }

    private void ExtendedSplash_OnResize(Object sender, WindowSizeChangedEventArgs e)
    {
        if (_splash != null)
        {
            _splashImageRect = _splash.ImageLocation;
            PositionImage();
            PositionRing();
        }
    }

    private async void DismissedEventHandler(SplashScreen sender, object e)
    {
        await SetupApp();
        await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, DismissExtendedSplash);
    }

    private async Task SetupApp()
    {
        await Task.Delay(2000);
    }

    private void DismissExtendedSplash()
    {
        _rootFrame.Navigate(typeof(MainPage));
    }
}
```

Only thing left to do is tell our app to use this extended splash screen. This needs to happen in `App.xaml.cs` in the `OnLaunched` method. We need to add a snippet of code right below these lines

```csharp
// Place the frame in the current Window
Window.Current.Content = rootFrame;
```

This is the snippet we need to add:

```csharp
if (e.PreviousExecutionState != ApplicationExecutionState.Running)
{
    ExtendedSplash extendedSplash = new ExtendedSplash(e.SplashScreen);
    rootFrame.Content = extendedSplash;
}
```

First we check if the app was not running before we got here. Next we initialise an instance of the ExtendedSplash page and pass it the splashscreen, finally we set that instance to be the content of the rootframe. This way we show our page on screen without it showing up in the navigation backstack.

For clarity, here's the complete `OnLaunched` method.

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    // Do not repeat app initialization when the Window already has content,
    // just ensure that the window is active
    if (!(Window.Current.Content is Frame rootFrame))
    {
        // Create a Frame to act as the navigation context and navigate to the first page
        rootFrame = new Frame();

        rootFrame.NavigationFailed += OnNavigationFailed;

        if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
        {
            //TODO: Load state from previously suspended application
        }

        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        if (e.PreviousExecutionState != ApplicationExecutionState.Running)
        {
            ExtendedSplash extendedSplash = new ExtendedSplash(e.SplashScreen);
            rootFrame.Content = extendedSplash;
        }
    }

    if (e.PrelaunchActivated == false)
    {
        if (rootFrame.Content == null)
        {
            // When the navigation stack isn't restored navigate to the first page,
            // configuring the new page by passing required information as a navigation
            // parameter
            rootFrame.Navigate(typeof(MainPage), e.Arguments);
        }
        // Ensure the current window is active
        Window.Current.Activate();
    }
}
```

If we launch the app at this point it will show the splashscreen for a very brief time and then switch to the extended splashscreen with the `ProgressRing`.

### Adding the connected animation
Don't worry, the hardest part is already over! Adding a connected animation is just two simple steps. We'll start with the `ExtendedSplash` page. We need to add one line of code right before we navigate away from the page.

```csharp
private void DismissExtendedSplash()
{
    ConnectedAnimationService.GetForCurrentView().PrepareToAnimate("image", ExtendedSplashImage);
    _rootFrame.Navigate(typeof(MainPage));
}
```

(`ConnectedAnimationService` lives in the `Windows.UI.Xaml.Media.Animation` namespace, don't forget to add it!)

Every UWP app has an app wide instance of `ConnectedAnimationService`. By calling its `PrepareToAnimate` method we register an image (ExtendedSplashImage) to a key ("image").

Now, for the other side of the equation, here's the XAML for MainPage.

```xml
<Page x:Class="AnimatedSplash.MainPage"
      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
      xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
      xmlns:local="using:AnimatedSplash"
      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
      mc:Ignorable="d">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Image x:Name="HeaderImage"
               Width="150"
               Margin="16,16,0,15.8"
               HorizontalAlignment="Left"
               Source="Assets/SplashScreen.png"
               Stretch="Uniform" />

        <TextBlock Grid.Column="1"
                   VerticalAlignment="Center"
                   FontSize="50"
                   FontWeight="Bold"
                   Style="{StaticResource TitleTextBlockStyle}"
                   Text="Composition demo" />
    </Grid>
</Page>
```

MainPage has an `Image` that shows the app logo in a smaller version in the upper left corner of the screen. My connected animation will animate from big and centered on the splashscreen to smaller and top left on the MainPage.

In MainPage.xaml.cs we need to override the `OnNavigatedTo` method.

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    base.OnNavigatedTo(e);
    ConnectedAnimation imageAnimation = ConnectedAnimationService.GetForCurrentView().GetAnimation("image");
    imageAnimation?.TryStart(HeaderImage);
}
```

We use the same `ConnectedAnimationService` to get the image animation that was registered in the ExtendedSplash page. Once we have that, we can start the animation and the system will take care of the rest!

### Does it have to be the same image?

No! as long as the source and target of the animation exist, the system will do its thing!

### Conclusion
In this post I've shown two things, how you can add an extended splash screen to your app, and how you can use a connected animation to fluently move from the splashscreen into the app.

Both of these things are equally important, an extended splashscreen is important for when your app needs some time to startup, it shows the user that the app is working on some stuff and not frozen. The animation is important because these fluent details give your app a professional, smooth look and feel.

The full code can be found on my <a href="https://github.com/NicoVermeir/AnimatedSplashscreenDemo" target="_blank">GitHub</a>

Happy coding!