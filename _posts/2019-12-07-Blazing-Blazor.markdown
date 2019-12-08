---
layout: post
title: Blazing Blazor
date: 2019-12-07 12:00:00 +0100
description: Netcore 3.1 was just released few days ago and it was released as long-term supported (LTS) release. It seems the right time to play with Blazor.
img: Blazing_Blazor.png
tags: [Blazor, Netcore, Web Development, Education, Software]
---

Netcore 3.1 was just [released few days ago](https://devblogs.microsoft.com/dotnet/announcing-net-core-3-1/) and it was released as long-term supported (LTS) release. It seems the right time to play with Blazor.

This article I will start a new Visual Studio Solution, I'll call it *BlazorPlayground*, and from the default project templates I'll will play around with the playground. Nothing to fancy.

You may see this article like a step by step guide, I intended to describe the steps that i took (just for my sanity check .P).

# Development Environment
So as an developement environment, i'm using a windows machine and i'm using:
* Visual Studio 2019
* Netcore 3.1

With all the defaults values setted.

# *BlazorPlayground* Solution
The solution is composed with: 
* *BlazorServer* project: Blazor application server side.
* *BlazorWebAssembly* project: Blazor application client side (webassembly).
* *ModelsAndAbstractions* project: Library with the pocos and interfaces (if any).
* *ServicesConsumed* project: Library with the application services.
* *CommonRazorComponents* project: Razor library with common components from both applications.

No new functionality will be added, the applications are just .... refactored.


# Step 1: Create Projects and structure
I'll start by creating the projects, their structure and reference them.
## *BlazorServer* project
* Create a new Solution and choose *Blazor App* project.

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/01-NewSolution.png)

* Set Project name as "BlazorServer".
* Set Solution name as "BlazorPlayground".

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/02-BlazorServerApp.png)

* Set Target Framework as "ASP.NET Core 3.1"
* Set Template as "Blazor Server App".

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/03-BlazorServerApp.png)

* Click Create.

## *BlazorWebAssembly* project
* Create a new Project and choose *Blazor App* project.

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/01-NewSolution.png)

* Set Project name as "BlazorWebAssembly".

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/04-BlazorWebAssemblyProject.png)

* Set Target Framework as "ASP.NET Core 3.1"
* Set Template as "Blazor WebAssembly App".

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/05-BlazorWebAssemblyApp.png)

* Click Create.

## Checkpoint 01
The Solution has two projects by now:

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/06-BlazorPlayground01.png)

And by executing the *BlazorServer* project:

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/07-BlazorServerWebApp.png)

And by executing the *BlazorWebAssembly* project:

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/08-BlazorWebAssemblyWebApp.png)

Everything is working without any issues.


## *ModelsAndAbstractions* project
* Create a new Project and choose *Class library(.Net Standard)* project.
* Set Project name as "ModelsAndAbstractions".

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/09-ModelsAndAbstractions.png)
* Click Create.
* Delete the new created "Class1.cs".
* Add Folder "Models" to the root.
* Add Folder "Abstractions" to the root.

## *ServicesConsumed* project
* Create a new Project and choose *Class library(.Net Standard)* project.
* Set Project name as "ServicesConsumed".

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/10-ServicesConsumed.png)
* Click Create.
* Delete the new created "Class1.cs"
* Add Folder "Services" to the root.

## Add References
* Add to __*BlazorServer*__ and __*BlazorWebAssembly*__ project references to the projects __*ModelsAndAbstractions*__ and __*ServicesConsumed*__.
* Add to __*ServicesConsumed*__ reference to __*ModelsAndAbstractions*__.

## Checkpoint 02
By now the solution as the following structure:
### Blazor Projects

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/11-Projects.png)
### Libraries

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/12-Libraries.png)

Hence only projects (empty) were added there is no need to check if both applications are still working. 

# Step 2: Extract Models and Services
On this step there are two course of actions. First I'll extract the models and services from the applications and put them in the right projects. Second, I have to fix the references to these object since they are no longer on the applications projects.

## Changes on *ModelsAndAbstractions* project
* Create a new class under Models and name it "WeatherForecast.cs"
* Copy the content of the same class of *BlazorServer/Data/WeatherForecast.cs*
```csharp
namespace ModelsAndAbstractions.Models
{
    public sealed class WeatherForecast
    {
        public DateTime Date { get; set; }

        public int TemperatureC { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);

        public string Summary { get; set; }
    }
}
```
## Changes on *ServicesConsumed* project
* Create a new class under Services and name it "WeatherForecastService.cs"
* Copy the content of the same class of *BlazorServer/Data/WeatherForecast.cs
```csharp
namespace ServicesConsumed.Services
{
    public sealed class WeatherForecastService
    {
        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
        };

        public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
        {
            var rng = new Random();
            return Task.FromResult(Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            }).ToArray());
        }
    }
}
```
## Changes on *BlazorServer* project
* Delete folder "Data" and all its content.
* Open Startup.cs and fix *WeatherForecastService* dependency
* Open Data/FetchData.razor and fix *WeatherForecastService* and *WeatherForecast* references.
```csharp
@using ModelsAndAbstractions.Models
@using ServicesConsumed.Services
```

## Changes on *BlazorWebAssembly* project
* Open Startup.cs and add *WeatherForecastService* dependency
```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddSingleton<WeatherForecastService>();
        }
```
* Replace Pages\FetchData.razor with the same content of BlazorServer\Pages\FetchData.razor

> With this change, we are no longer loading the json file.

## Checkpoint 03
* Rebuild the Solution
* Start each *BlazorServer* and *BlazorWebAssembly*, verify everything is still working as before.


# Step 3: Extracting Common Features
From both projects I can see 3 common component in /Pages:
* Counter.razor
* FetchData.razor
* Index.razor

So they will be extracted and created in a new library.

## Razor Components Library
* So create a new Project and choose *Razor Class library* project.

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/13-RazorProject.png)

* Set Project name as "CommonRazorComponents".

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/15-RazorProject2.png)

* Click Create.

The Project structure looks like this:

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/16-CommonRazorComponentsStructure.png)

* Delete:
    * wwwroot/background.png
    * wwwroot/exampleJsInterop.js
    * wwwroot/styles.css
    * Component1.razor
    * ExampleJsInterop.cs
* Copy from *BlazorServer* (or *BlazorWebAssembly*) the razor files Counter.razor, FetchData.razor and Index.razor
* Remove these files from both Blazor projects.
* Add to both projects reference to *CommonRazorComponents* project.
* Add to *CommonRazorComponents* reference to *ModelsAndAbstractions* and *ServicesConsumed* projects.


## Checkpoint 04
* Rebuild the Solution
* Start each *BlazorServer* and *BlazorWebAssembly*, verify everything is still working.

tick<br/>
tack<br/>
tick<br/>
tack<br/>
tick<br/>
tack<br/>
tick<br/>
tack<br/>
tick<br/>
tack<br/>

yeah .....  not so much. So none of the links can be "found".

## Challenge #01: Application Routing
So we have the pages, they were shown before they were moves, so .... let us go back to the [official documentation](https://docs.microsoft.com/en-us/aspnet/core/blazor/routing?view=aspnetcore-3.1):
> Use the AdditionalAssemblies parameter to specify additional assemblies for the Router component to consider when searching for routable components. Specified assemblies are considered in addition to the AppAssembly-specified assembly.

> In the following example, Component1 is a routable component defined in a referenced class library. The following AdditionalAssemblies example results in routing support for Component1:

```html
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    <!-- 
        ...
    -->s  
</Router>
```

*AdditionalAssemblies* expects *System.Collections.Generic.IEnumerable<System.Reflection.Assembly>* so I have to get the System.Reflection.Assembly from the razor library with the common components.


## Challenge #01: Fixing Application Routing
* Open App.razor and add the additional assemblies:
```html
<Router AppAssembly="@typeof(Program).Assembly"
        AdditionalAssemblies="new[] { typeof(CommonRazorComponents.Index).Assembly }">
        <!-- 
            ...
        -->
</Router>
```

## Checkpoint 05
* Rebuild the Solution
* Start each *BlazorServer* and *BlazorWebAssembly*, verify everything is still working.

# Step 4: Self Contained Razor Library (CommonRazorComponents)
So now I have a razor library with the common components for both applications. That is great but there is something that is bothering me... the components have css classes that I didn't defined and, although it is working and that was the expected, I'm depending that the consumer for my library has the right css classes defined.

Yes, I could documented this requirements and say "You have to use bootstrap version X.P.T.O to work", but in reality I don't want the consumer to worry about it and I care less which CSS libraries he will use it.

It's my component and I responsable for it, period. So I'll self contain the component library.

## Static content
From the [official documentation](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/ui-class?view=aspnetcore-3.1&tabs=visual-studio#create-an-rcl-with-static-assets):
> To include companion assets as part of an RCL, create a wwwroot folder in the class library and include any required files in that folder.
> 
> When packing an RCL, all companion assets in the wwwroot folder are automatically included in the package.

### Identify css classes
There are two components with "references" to css classes:
* Counter.razor: class="btn btn-primary"
* FetchData.razor: class="table"

I'll create two css files under the CommonRazorComponents/wwwroot/ folder:
* counter.css
* fetchdata.css

And on each file i'll create the classes prefixed with the component name. Why? So thet wont be accidentaly overridden by some definition or library.

Copy paste the identified classes from __*bootstrap.min.css*__ into each file.

Rename classes:
* counter.css
    * btn ==> counter-btn
    * btn-primary ==> counter-btn-primary
* fetchdata.css
    * table ==> fetchdata-table

Refactor components to reference the new classes:
* Counter.razor: class="counter-btn counter-btn-primary"
* FetchData.razor: class="fetchdata-table"

Load each css file in the right component:
* Counter.razor: <link href="_content/CommonRazorComponents/counter.css" rel="stylesheet" />
* FetchData.razor: <link href="_content/CommonRazorComponents/fetchdata.css" rel="stylesheet" />

**What are these links struture? They shouldn't be refence to wwwroot?**

Yeah, that was what i though until it fail loading the css files and [get back to the documentation](https://docs.microsoft.com/en-us/aspnet/core/razor-pages/ui-class?view=aspnetcore-3.1&tabs=visual-studio#consume-content-from-a-referenced-rcl
).
> The files included in the wwwroot folder of the RCL are exposed to the consuming app under the prefix _content/{LIBRARY NAME}/. For example, a library named Razor.Class.Lib results in a path to static content at _content/Razor.Class.Lib/.

Because it is a library, because it is referenced, it has to be like this.

## Checkpoint 06
* Rebuild the Solution
* Start each *BlazorServer* and *BlazorWebAssembly*, verify everything is still working.

# Blazor Playgroung Solution Relations
By now the solution should be working and decoupled:
* BlazorServer references
    * ModelsAndAbstractions
    * ServicesConsumed
    * CommonRazorComponents
* BlazorWebAssembly references
    * ModelsAndAbstractions
    * ServicesConsumed
    * CommonRazorComponents
* ModelsAndAbstractions refences
    * No one: It is a classes and interfaces library.
* ServicesConsumed references
    * ModelsAndAbstractions
* CommonRazorComponents
    * ModelsAndAbstractions
    * ServicesConsumed

![Blazing Blazor]({{site.baseurl}}/assets/img/BlazingBlazor/17-BlazingBlazorRelations.png)

Blazor applications refers the libraries that they need (for DI, composition, etc).

CommonRazorComponents library refers only the libraries they need for the common components.

ServicesConsumed library refers only the models library.

# Conclusion
In this post I refactor both Blazor applications, server and webassembly, to play around the features, configurations and getting the felling about the componentization.

I extracted the models, the services and then the common components so it will become decoupled.

# What's Next?
Now that we have two blazor applications (server and webassembly) working, the next post it will be about the differences between them.

Any question? I'll do my best to help you.

# Resources
* [2019-12-06] https://devblogs.microsoft.com/dotnet/announcing-net-core-3-1/  
* [2019-12-06] https://docs.microsoft.com/en-us/aspnet/core/blazor/routing?view=aspnetcore-3.1
* [2019-12-07] https://chrissainty.com/introduction-to-routing-in-blazor/
* [2019-12-07] https://docs.microsoft.com/en-us/aspnet/core/blazor/class-libraries?view=aspnetcore-3.1&tabs=visual-studio
* [2019-12-07] https://docs.microsoft.com/en-us/aspnet/core/razor-pages/ui-class?view=aspnetcore-3.1&tabs=visual-studio