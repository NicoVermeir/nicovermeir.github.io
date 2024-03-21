---
layout: single
title:  "Setting generated project name in .NET Aspire"
author: Nico Vermeir
date:   2024-03-21 12:59:18 +0000
categories: aspire
---

## Intro
We have been building a microservices application with .NET Aspire. With .NET Aspire you get an AppHost project that references all of your services and web applications. The AppHost project, by default, generates a `Projects` namespace with a class for every project that the AppHost references. This is a great way to access your services and web applications in a strongly typed way. 

Let's assume a .NET Aspire project with a web application called `WebApp` and a service called `Service`. The generated Projects namespace will look like this:

```csharp
namespace Projects;

[global::System.Diagnostics.DebuggerDisplay("Type = {GetType().Name,nq}, ProjectPath = {ProjectPath}")]
public class Service : global::Aspire.Hosting.IProjectMetadata
{
  public string ProjectPath => """d:\projects\AspireDemo\Service\Service.csproj""";
}
```

In the AppHost project, you can now access the `Service` project like this:

```csharp
var service = builder.AddProject<Projects.Service>("service-api");

builder.AddProject<Projects.AspireDemo_Web>("webfrontend")
    .WithReference(service);
```

As you can see, pretty nifty usage of code generators to provide a strong-typed way of hooking services up to each other.

## The issue
However, in our microservices based application we ran into the issue that we had multiple services with the same name. Apparently the code generators in .NET Aspire only generate the class for one of the two. I've reproduced this issue in a project for demo purposes.

Given this solution

 ![solution explorer](/assets/images/posts/20240321/001.png)

 These are the classes generated in the `Projects` namespace:

  ![generated projects](/assets/images/posts/20240321/002.png)

Only one project to be found. Our initial workaround was using the `WithReference` method with the project path instead of the generated class. This works, but it's not as clean as using the generated class. Luckily there is a change that we can make in the `csproj` file of the AppHost to fix this.

By adding `AspireProjectMetadataTypeName` to the project references, we can specify the name of the generated class. This way we can have multiple services with the same name in the AppHost project.
  
```xml
<ItemGroup>
  <ProjectReference Include="..\AspireDemo.Microservice1\Presentation.Api\Presentation.Api.csproj" AspireProjectMetadataTypeName="MicroService1" />

  <ProjectReference Include="..\AspireDemo.Microservice2\Presentation.Api\Presentation.Api.csproj" AspireProjectMetadataTypeName="MicroService2" />

  <ProjectReference Include="..\AspireDemo.Web\AspireDemo.Web.csproj" />
</ItemGroup>
   ```


After adding the `AspireProjectMetadataTypeName` attribute to the `ProjectReference` elements, the generated classes will look like this:

  ![generated projects](/assets/images/posts/20240321/003.png)

Exactly what we needed. Now we can reference our services in a strongly typed way in the AppHost project.

## Conclusion
.NET Aspire is a great way to build microservices applications. The generated `Projects` namespace provides a great help to hook up your different services in a strongly typed way. 

However, when you have multiple services with the same name, you'll run into issues. 

By adding the `AspireProjectMetadataTypeName` attribute to the `ProjectReference` elements in the AppHost project, you can specify the name of the generated class. This way you can have multiple services with the same name in the AppHost project.

Happy coding!