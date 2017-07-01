---
layout: post
title: Android Floating Widgets In Xamarin
date: 2017-07-01
comments: true
published: true
categories: ["post"]
tags: [".Net", "Xamarin", "Android"]
author: Nico Vermeir
---
I first noticed the floating widgets when I saw Facebook Messenger on a Samsung. I really liked the idea of the chatheads with the conversation folding out without having to launch the app first. After some experimenting I managed to get it working in Xamarin Android. I'll walk you through it in this article.

### Project setup
We start of with an empty Xamarin Droid project. Next, we'll need an image to show in the widget. I made a circle with my profile picture to mimic the Messenger chatheads. I've put the png in drawable-nodpi. This drawable folder contains images that are valid for every screensize but can still be overwritten by more specific versions in the other drawable folders.

### What is a floating widget?
A floating widget on Android is basically nothing more than a loaded View that's drawn on top of everything else. To be able to do this we need to add a Service to our app. A service is a component that can do work without having an active UI, meaning that it can inflate views from a background worker (for example: creating a floating widget when a push notification arrives). In this sample we'll let the MainActivity start the service and then finish itself.

### The layout
We'll start by creating the layout for the widget. Add an axml file to Resources/layout and name it layout\_floating_widget.axml

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">

    <RelativeLayout
        android:id="@+id/root"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content">

        <RelativeLayout
            android:id="@+id/icon"
            android:layout_width="wrap_content"
            android:visibility="visible"
            android:layout_height="wrap_content">
            <ImageView
                android:layout_width="60dp"
                android:layout_height="60dp"
                android:layout_marginTop="8dp"
                android:src="@drawable/chathead" />
        </RelativeLayout>
    </RelativeLayout>
</FrameLayout>
```

This layout is basically an image view that shows the chathead.png that lives in the drawable-nodpi folder.

### Creating the service
The service will be responsible for creating the widget, and for responding to touch events like a tap and a drag.

Add a folder called 'Services' to the project. Create a new class called FloatingWidgetService in that folder. Normally in Android service classes need to be declared in the manifest before being useable as a service. In Xamarin it suffices to decorate the class with the Service attribute. The class also needs to inherit from the Service base class to be able to implement the service lifecycle methods.

```csharp
[Service]
public class FloatingWidgetService : Service
{
```

Now we add some private fields to the class.

```csharp
private IWindowManager _windowManager;
private WindowManagerLayoutParams _layoutParams;
private View _floatingView;
```

The IWindowManager is a system-wide service on Android that handles the creation and order of drawing of views.
The WindowManagerLayoutParams is a class that provides a set of properties that the IWindowManager can apply to a View. In this case, it will set the x / y coordinates for the floating view.

Next, we'll override the OnCreate method.

```csharp
public override void OnCreate()
{
    base.OnCreate();

    _floatingView = LayoutInflater.From(this).Inflate(Resource.Layout.layout_floating_widget, null);
    
    _layoutParams = new WindowManagerLayoutParams(
        ViewGroup.LayoutParams.WrapContent,
        ViewGroup.LayoutParams.WrapContent,
        WindowManagerTypes.Phone,
        WindowManagerFlags.NotFocusable,
        Format.Translucent)
    {
        Gravity = GravityFlags.Left | GravityFlags.Top
    };

    _windowManager = GetSystemService(WindowService).JavaCast<IWindowManager>();
    _windowManager.AddView(_floatingView, _layoutParams);
}
```

In the OnCreate method we inflate the layout for the widget and we define the layout parameters. The overload that we use take the following parameters:
width, height, type, flags and format.

Width and height are pretty obvious. The Phone type is used for non-application windows, mostly for incoming calls. Layouts of this type are rendered on top of everything else. The NotFocusable flag means that the layout can ignore any layout changes from input methods, like the soft keyboard popping up. The Gravity flags make sure that the widget will appear in the top left of the screen.

Finally, we get a reference to the WindowManager and we use it to add the floating widget to the window.

We also want to remove the widget from the screen if the service instance is destroyed.

```csharp
public override void OnDestroy()
{
    base.OnDestroy();

    if (_floatingView != null)
    {
        _windowManager.RemoveView(_floatingView);
    }
}
```

The complete class looks like this

```csharp
[Service]
public class FloatingWidgetService : Service
{
    private IWindowManager _windowManager;
    private WindowManagerLayoutParams _layoutParams;
    private View _floatingView;

    public override void OnCreate()
    {
        base.OnCreate();

        _floatingView = LayoutInflater.From(this).Inflate(Resource.Layout.layout_floating_widget, null);
        
        _layoutParams = new WindowManagerLayoutParams(
            ViewGroup.LayoutParams.WrapContent,
            ViewGroup.LayoutParams.WrapContent,
            WindowManagerTypes.Phone,
            WindowManagerFlags.NotFocusable,
            Format.Translucent)
        {
            Gravity = GravityFlags.Left | GravityFlags.Top
        };

        _windowManager = GetSystemService(WindowService).JavaCast<IWindowManager>();
        _windowManager.AddView(_floatingView, _layoutParams);
    }
    
    public override IBinder OnBind(Intent intent)
    {
        return null;
    }

    public override void OnDestroy()
    {
        base.OnDestroy();

        if (_floatingView != null)
        {
            _windowManager.RemoveView(_floatingView);
        }
    }
}
```

### Consuming the service
Using the service we just created is pretty simple. Open MainActivity.cs and change the OnCreate method to this.

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    
    SetContentView(Resource.Layout.Main);

    StartService(new Intent(this, typeof(FloatingWidgetService)));
    Finish();
}
```

The StartService method comes from the Activity base class. We pass it an intent of the type of our service. After that, we finish the MainActivity. This will cause the app to close, but the floating widget will be visible.

If we try to run the app now, it will crash with the following exception:

```txt
Unhandled Exception:

Android.Views.WindowManagerBadTokenException: Unable to add window android.view.ViewRootImpl$W@87dfeae -- permission denied for this window type
```

It looks like we're missing a permission. Open the project properties, go to the Android Manifest tab and from the list of Required Permissions check SYSTEM\_ALERT_WINDOW. If you run the app again, it should look something like this

![](http:\\www.spikie.be\images\floatingwidget\01.PNG)

we're off to a great start! Now we need to be able to move the widget around.

### Reacting to touch events
To react to touch events we need to adjust our service. The service class will need to act as a touch event listener. Once it can do that, we can pass it to a View that will pass the touch event to the event listener.

To create a touch event listener we need a class that implements View.IOnTouchListener. As mentioned, we'll use the FloatingWidgetService for this.

```csharp
[Service]
public class FloatingWidgetService : Service, View.IOnTouchListener
{
```

The IOnTouchListener interface contains one member

```csharp
bool OnTouch(View view, MotionEvent motion)
```

But before we start implementing this method, we'll need some more fields.

```csharp
private int _initialX;
private int _initialY;
private float _initialTouchX;
private float _initialTouchY;
```

Next, on to the OnTouch method

```csharp
public bool OnTouch(View view, MotionEvent motion)
{
    switch (motion.Action)
    {
        case MotionEventActions.Down:
            //initial position
            _initialX = _layoutParams.X;
            _initialY = _layoutParams.Y;

            //touch point
            _initialTouchX = motion.RawX;
            _initialTouchY = motion.RawY;
            return true;

        case MotionEventActions.Move:
            //Calculate the X and Y coordinates of the view.
            _layoutParams.X = _initialX + (int)(motion.RawX - _initialTouchX);
            _layoutParams.Y = _initialY + (int)(motion.RawY - _initialTouchY);

            //Update the layout with new X & Y coordinate
            _windowManager.UpdateViewLayout(_floatingView, _layoutParams);
            return true;
    }

    return false;
}
```

the MotionEvent parameter contains an Action property that holds the detected touch event. For now we'll only need the Down and Move event.

When the Down event is detected we'll grab the current location of the widget in coordinates and the current touch point. We'll need these in case that the user is going to drag the widget around.

When the Move event is detected we'll have gone through the Down event already, so the fields should be filled in. We'll calculate the new position of the widget and pass the view and layout parameters to the windowmanager so it can update the location of the view.

This completes our touch listener. Now we need to hook it up to the view.

```csharp
private void SetTouchListener()
{
    var rootContainer = _floatingView.FindViewById<RelativeLayout>(Resource.Id.root);
    rootContainer.SetOnTouchListener(this);
}
```

First we grab a reference to the RelativeLayout called root, and then we use the current instance of the service class as a touch listener for the rootContainer view. Last thing to do is call this method from the OnCreate method, right after inflating the view.

```csharp
public override void OnCreate()
{
    base.OnCreate();

    _floatingView = LayoutInflater.From(this).Inflate(Resource.Layout.layout_floating_widget, null);
    SetTouchListener();
    ...
```

When you run the app now, you'll be able to drag the widget around the screen.

### Expanding / Collapsing the widget
The greatest thing about the Messenger chatheads is that they can expand and show the entire conversation. And we've got mostly everything in place to add this to our widget as well.

First, we'll need to add the flyout to our widget layout in axml. For this example I'm using a LinearLayout with two mocked chat responses, which is good enough to get the point across.

Open layout\_floating_widget.axml and add this snippet in the root container, underneath the RelativeLayout called 'icon'.

```xml
<LinearLayout
    android:id="@+id/flyout"
    android:layout_marginTop="50dp"
    android:layout_marginLeft="20dp"
    android:layout_marginRight="20dp"
    android:visibility="gone"
    android:layout_marginBottom="100dp"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:background="#FFFFFF"
    android:padding="8dp">
    <RelativeLayout
        android:layout_width="match_parent"
        android:background="#FF0026FF"
        android:layout_marginRight="32dp"
        android:layout_height="50dp">
        <TextView
            android:id="@+id/txtMessage"
            android:layout_width="wrap_content"
            android:layout_marginRight="16dp"
            android:text="Hello"
            android:layout_height="wrap_content"
            android:textColor="@android:color/black"
            android:maxWidth="240dp" />
    </RelativeLayout>

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_marginTop="16dp"
        android:layout_marginLeft="32dp"
        android:background="#FF0094FF"
        android:layout_height="50dp">
            <TextView
                android:id="@+id/txtMessage"
                android:layout_width="wrap_content"
                android:layout_marginRight="16dp"
                android:text="Hi!"
                android:layout_height="wrap_content"
                android:textColor="@android:color/black"
                android:maxWidth="240dp" />
    </RelativeLayout>
</LinearLayout>
```

As you can see, the visibility of this LinearLayout is set to gone, so that when the widget shows up, it will be collapsed. We need to toggle this visibility to expand / collapse the widget.

Toggling is done on touch, we already have the touch listener setup, so we'll just need to extend it a bit. 

Open the FloatingWidgetService again and add a field to hold a reference to the flyout.

```csharp
private View _expandedContainer;
```

In the OnCreate method, add this as the last line of the method.

```csharp
_expandedContainer = _floatingView.FindViewById(Resource.Id.flyout);
```

In the OnTouch method, add an extra case to the switch statement.

```csharp
case MotionEventActions.Up:
    int offsetX = (int)motion.RawX - (int)_initialTouchX;
    int offsetY = (int)motion.RawY - (int)_initialTouchY;

    if (offsetX < 10 && offsetY < 10)
    {
        _expandedContainer.Visibility = _expandedContainer.Visibility == ViewStates.Gone ?
            ViewStates.Visible :
            ViewStates.Gone;
    }
    return true;
```

The problem with using the up event to detect a 'click' is that the event will also trigger after a move, which we don't want. To prevent this we calculate the X and Y offset, the distance travelled between the down and the up event. If it's smaller than 10 (just a value I picked, experiment a bit to find the value that suites you best) we consider it a tap or click event. When a click is detected, we toggle the visibility of the flyout.

Run the app, you'll notice that you can still safely drag the widget around and expand / collapse it by tapping on it.

![](http:\\www.spikie.be\images\floatingwidget\02.PNG)

### Conclusion
In this article I've shown the steps required to create a floating widget in Xamarin Android. Floating widgets can provide value but use them with care. I would definitely advice you to add an option in your app's settings to toggle them off completely. Not everyone seems to like them.

The demo code can be found <a href="http:\\www.spikie.be\images\floatingwidget\demo.zip" target="_blank">here</a>.