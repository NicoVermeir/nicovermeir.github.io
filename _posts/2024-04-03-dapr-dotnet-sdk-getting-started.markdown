---
layout: single
title:  "Getting started with the Dapr .NET SDK"
author: Nico Vermeir
date:   2024-04-03 12:59:18 +0000
categories: dapr
---

## Intro
In this post, we'll take a look at the Dapr .NET SDK. Dapr is a portable, event-driven runtime that makes it easy for developers to build resilient, microservices applications. 

Dapr can be used with any language that can make HTTP requests. However, Dapr also provides SDKs for Go, Java, .NET, Python, and JavaScript. These SDKs provide a more programming language-driven way to interact with Dapr. However, documentation on the .NET SDK is a bit sparse. In this post, we'll take a look at how to get started with the Dapr .NET SDK.

## Prerequisites
This post assumes that you already have Dapr installed. If you don't have Dapr installed yet, you can follow the instructions on the [Dapr website](https://docs.dapr.io/getting-started/).

## Setting up the project
For this post we'll be using a simple setup with an ASP.net minimal API as a backend, and a Blazor application as frontend. The demo solution looks like this:

![Starter solution](/assets/images/posts/20240403/001.png)

Nothing new so far, just File > New project in Visual Studio. Next we want to setup these projects to have a Dapr sidecar.

## Sidecars
A sidecar in software development is an architectural pattern where a container has a helper container running alongside it. The helper container is responsible for handling a specific task. In the case of Dapr, the sidecar is responsible for handling all the Dapr runtime features. This way your application doesn't have to worry about the Dapr runtime. Or, as explained on the Microsoft architecture website:

> Deploy components of an application into a separate process or container to provide isolation and encapsulation. This pattern can also enable applications to be composed of heterogeneous components and technologies.
>
> This pattern is named Sidecar because it resembles a sidecar attached to a motorcycle. In the pattern, the sidecar is attached to a parent application and provides supporting features for the application. The sidecar also shares the same lifecycle as the parent application, being created and retired alongside the parent. The sidecar pattern is sometimes referred to as the sidekick pattern and is a decomposition pattern.

Source: https://learn.microsoft.com/en-us/azure/architecture/patterns/sidecar

## Running a Dapr-enabled platform
Next, we need a way to launch everything within the Dapr context, so that Dapr knows it needs to start the sidecars. This is usually done by create a `dapr.yaml` file. A `dapr.yaml` file in the root of our solution. For our example it could look like this:

```yaml
apps:
- appDirPath: DaprDemo.Api
  appID: daprdemo-api
  appPort: 5105
  command:
  - dotnet
  - run
- appDirPath: DaprDemo.Client
  appID: daprdemo-client
  appPort: 5240
  command:
  - dotnet
  - run
version: 1
```
In the `dapr.yaml` file we specify all our services, their ports and an appID. The appID is used by Dapr to identify the service.

Next we can run our entire set of Dapr-enabled services by using the Dapr CLI:

`dapr run -f dapr.yaml`

Which nets these results:
![dapr run output](/assets/images/posts/20240403/002.png)

Everything is up and running!

## Using Dapr in your .NET application
Now that we have our services running in the Dapr context, we can start using Dapr in our .NET application. The Dapr .NET SDK is available as a NuGet package. You can install it by running this command for our client project:

`dotnet add package Dapr.AspNetCore`

In this example we will modify the existing weather forecast sample page in the Blazor application to use Dapr. We'll use this one since this is used as an example in both the minimal API and Blazor templates.

The Dapr .NET SDK for ASP.net Core provides a `DaprClient` class that can be used to interact with the Dapr runtime. The `DaprClient` class provides methods to invoke Dapr service-to-service invocation, state management, pub/sub, and bindings. Dapr provides an easy extension method to register that client in the DI container. In the `Program.cs` file of the Blazor application, we can add the following line:

```csharp
builder.Services.AddDaprClient();
```
Once this is done we can inject the DaprClient in `Weather.razor`.

```csharp
public List<WeatherForecast> Forecasts { get; set; }

[Inject]
public DaprClient Client { get; set; }

protected override async Task OnInitializedAsync()
{
    Forecasts = await Client.InvokeMethodAsync<List<WeatherForecast>>(HttpMethod.Get, "daprdemo-api", "weatherforecast");
}
```

Using the `InvokeMethodAsync` method we can call the `weatherforecast` endpoint of the `daprdemo-api` service. The `InvokeMethodAsync` method takes the HTTP method, the appID of the service, and the endpoint to call. It also specifies what the expected result type is. The Dapr .NET SDK will handle the call to the backend, via the Dapr sidecar, and deserializes the result.

If we now run the project again using the Dapr CLI, and navigate to the weather page, we should see the weather forecast data provided by the backend.

## Running and debugging from Visual Studio
So far, we have been developing a Dapr-based platform, using the Dapr CLI to run our applications. However, this is not the experience we as .NET developers are used to. We want to be able to run our services from Visual Studio. Luckily, Microsoft has provided a Visual Studio extension that does this. The extension is open-source and can be found on GitHub:
https://github.com/microsoft/vs-dapr. The extension is still in preview, but I found it to work quite well.

Once installed, we will get a right-click menu item on solution level to generate a `dapr.yaml` file. This file will be generated based on the projects in the solution. 

![New menu item](/assets/images/posts/20240403/003.png)

The `dapr.yaml` file will be generated in a virtual Dapr project in the Solution Explorer. This project can be set as startup project, When you press F5 in Visual Studio you will see the debugger launching. In the background all of your services are launching, together with their Dapr sidecars.

![Solution with Dapr project](/assets/images/posts/20240403/004.png)

What the extension doesn't do so far is actually launching a browser window for your frontend or Swagger page. But we know the ports it's running on, so we can navigate to the correct URL ourselves.

## Conclusion
In this post, we've seen how to get started with the Dapr .NET SDK. We've seen how to setup a Dapr-enabled platform, how to use Dapr in your .NET application, and how to run and debug your Dapr-enabled platform from Visual Studio. The Dapr .NET SDK provides a more programming language-driven way to interact with Dapr, and the Dapr CLI provides an easy way to run your Dapr-enabled platform. The Visual Studio extension makes it easy to run and debug your Dapr-enabled platform from Visual Studio. 