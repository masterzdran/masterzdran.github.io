---
layout: post
title: Blazing Blazor - Part II
date: 2019-12-17 12:00:00 +0100
description: What is different between Blazor Server and Blazor WebAssembly projects.
img: Blazing_Blazor.png
cover: Blazing_Blazor_Part_II-cover.png
tags: [Blazor, Netcore, Web Development, Education, Software]
---

In my [last post about Blazor](https://masterzdran.github.io/Blazing-Blazor/), I walk throught the projects and extrated common features so both project are the same. I didn't add any feature (in fact i removed one) but I finish with two similar blazor apps, one server side and the other in WebAssembly.

In this post, I'll look up both projects and show what are the differences between them, now that they are decoupled.

# Development Environment
So as an developement environment, i'm using a windows machine and i'm using:
* Visual Studio 2019
* Netcore 3.1

With all the defaults values setted.

# Recap *BlazorPlayground* Solution
The solution is composed with: 
* *BlazorServer* project: Blazor application server side.
* *BlazorWebAssembly* project: Blazor application client side (webassembly).
* *ModelsAndAbstractions* project: Library with the pocos and interfaces (if any).
* *ServicesConsumed* project: Library with the application services.
* *CommonRazorComponents* project: Razor library with common components from both applications.

No new functionality was added, the applications was just .... refactored.


# From the begining
Both projects are netcore web applications, regardless of the techonology applied, so my first step is to create a default netcore web MVC application. Then I'll start comparing MVC vs Blazor Server and Blazor Server vs Blazor WebAssembly and figure it out the differences.

# Find the Differences

## Web MVC Application vs Blazor server
Between this two types of projects I'll only compare the startup and configuration between them, so it become more clearly the differences.

### CSPROJ
* Both projects are **Microsoft.NET.Sdk.Web** projects 
* Both target **netcoreapp3.1** framework. 

### Program.cs
* Both project have the same initiation CreateHostBuilder using Startup.

```
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

### Startup.cs
This file has more differences than the **Program.cs**

#### MVC Web Application
Add support to controllers and views.
```
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
}
```

Definition of the supported endpoints.

```
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    // ...  All similar code.
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

#### Blazor server
Add support to razor pages and server side blazor.
```
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
    services.AddServerSideBlazor();
}
```

Definition of the supported endpoints.

```
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    // ...  All similar code.
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapBlazorHub();
                endpoints.MapFallbackToPage("/_Host");
            });
}
```

## Blazor server vs Blazor Web Assembly
Now I'll check the differences between the Blazor flavoured apps.

### CSPROJ
* Both projects are **Microsoft.NET.Sdk.Web** projects.
* Blazor server target **netcoreapp3.1** framework.
* Blazor Web Assembly target **netstandard2.1** framework.
* Blazor Web Assembly use **RazorLangVersion** version 3.0.
* Blazor Web Assembly has dependencies (version 3.1.0-preview4.19579.2):
    * Microsoft.AspNetCore.Blazor
    * Microsoft.AspNetCore.Blazor.Build
    * Microsoft.AspNetCore.Blazor.HttpClient
    * Microsoft.AspNetCore.Blazor.DevServer

### Program.cs
Blazor Web Assembly has a different host from Blazor Server:
```
public static IWebAssemblyHostBuilder CreateHostBuilder(string[] args) =>
    BlazorWebAssemblyHost.CreateDefaultBuilder()
        .UseBlazorStartup<Startup>();
```
### Startup.cs
Blazor Web Assembly has a less complex definition than Blazor Server:
```
public void ConfigureServices(IServiceCollection services)
{
}

public void Configure(IComponentsApplicationBuilder app)
{
    app.AddComponent<App>("app");
}
```

### Application Index
* Blazor Server index page is Pages/_Host.cshtml.
* Blazor Server loads App component in ServerPrerendered render mode.
* Blazor Server load blazor.server.js script.
* Blazor Web Assembly index page is wwwroot/index.html.
* Blazor Web Assembly load blazor.webassembly.js script.


# What is Similar
* **App.razor** has the application component.
    * **RouteView** has a **DefaultLayout**.
    * **DefaultLayout** is any defined layout that inherits from **LayoutComponentBase**.
* **_Imports.razor** have the namespace imports to be used within the components.
* **wwwroot** has the static content of the application.
* **Shared** folder (by convention) has the components shared by the application.
    * The namespace can be referenced in the **_Imports.razor** file.
* **Pages** folder (by convention) has the routing pages of the application.
    * The namespace can be referenced in the **_Imports.razor** file.


# Conclusion
This is a short post where I analyzed the projects structure and figure it out the differences between the **Blazor Server app** and the **Blazor WebAssembly app**. Start comparing the **Web MVC** application structure with **Blazor Server** application and then the **Blazor Server app** and the **Blazor WebAssembly app**, highligthing the diferences and the similaritie.


# Source Code
All the code can be reached at https://github.com/masterzdran/BlazorPlayground

# Resources
* [2019-12-06] https://devblogs.microsoft.com/dotnet/announcing-net-core-3-1/  
* [2019-12-06] https://docs.microsoft.com/en-us/aspnet/core/blazor/routing?view=aspnetcore-3.1
* [2019-12-07] https://chrissainty.com/introduction-to-routing-in-blazor/
* [2019-12-07] https://docs.microsoft.com/en-us/aspnet/core/blazor/class-libraries?view=aspnetcore-3.1&tabs=visual-studio
* [2019-12-07] https://docs.microsoft.com/en-us/aspnet/core/razor-pages/ui-class?view=aspnetcore-3.1&tabs=visual-studio