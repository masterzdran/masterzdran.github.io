---
layout: post
title: OrchardCore, CMS for ASP.NET Core (Part 02).
date: 2020-06-10 12:00:00 +0100
description: Open-source, modular, multi-tenant application framework and CMS for ASP.NET Core.
img-remote: "yes"
img: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/img/orchard-logo.png
cover: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/img/OrchardCore.02.png
tags:
  [
    Visual Studio,
    Development,
    Education,
    Software,
    OrchardCore,
    CMS,
    dotnet,
    netcore,
    Jetbrains,
    Rider,
  ]
---

In my last article I started by creating a CMS project from scratch and suggested that you explore the several recipes and check their purposes. 

# Posts Planning
This set of posts will cover several parts of the Orchard Core components and, hopefully, by the end it will become much clearer how all of them interact with each other. So for now, the planning is:
* Part 01: The basic Orchard Core project. 
* Part 02: Create a Orchard Core Theme. (This article)
* Part 03: Create a Orchard Core Recipe.
* Part 04: Create a Orchard Core Module.
...


# My Development Environment
As part of my learning path, because I'm learning how to develop with Orchard and i'm working with dotnet, I perform my proof of concept on: 
* MacBook Pro (Retina, 13-inch, Late 2012)
  * Running MacOS Catalina (10.15.5)
* For IDE, I'm using:
  * Jetbrains Rider 10.15.5 (19F101)
  * Visual Studio Community 2019 for Mac (Preview)
    * Version 8.7 Preview (8.7 build 1077)

Why am I using both Rider and Visual Studio for Mac? Well, I decided to try it out with the Rider, however from time to time this IDE becomes possessed by the Evil and presents an erratic behavior (eg: in renaming files, doesn't allow fill the name). So since I'm still in the beginning, I'm alterning between Rider and the VS for Mac, but VS for MAC isn't VS for Windows. Rider isn't like VS for Windows either but seems more alike than VS for Mac.... and I have a license for Rider :).


# Before I start
I'm going to pick up a bootstrap theme. So I went to https://startbootstrap.com, I chose the [Freelancer theme](https://startbootstrap.com/themes/freelancer/) and  downloaded it. I'll need it later.

Themes in Orchard Core come in two flavours:
- Liquid templates
- Razor templates

I don't feel comfortable working with Liquid so I'll work with Razor instead.


# Getting Started

I will create a new netcore library for this theme:
![Orchard Core Theme][orchardcore.theme.01]

Now I delete the default Class1.cs and get an really empty project. :)

![Orchard Core Theme][orchardcore.theme.02]

Since we building a Razor template/theme, I need to edit the csproj and update the 
```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
    </PropertyGroup>
```
to
```xml
<Project Sdk="Microsoft.NET.Sdk.Razor">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <AddRazorSupportForMvc>true</AddRazorSupportForMvc>
    </PropertyGroup>
```


## Manifest.cs
Each theme has a manifest where it describes the theme itself. So at the root of the project I'll create a new class named Manifest.cs and add and update the following content:
```csharp
using OrchardCore.DisplayManagement.Manifest;

[assembly: Theme(
    Name = "Freelancer",
    Author = "Nuno Cancelo",
    Website = "http://masterzdran.github.io/",
    Version = "1.0.0",
    Description = "Freelance based theme for Orchard Core."
)]
```

By now we get several errors related to the "Cannot resolve symbol 'OrchardCore'", because I'm missing a few nuget references. So we have to add the following nuget:
* OrchardCore.Theme.Targets (For theme integration)
* OrchardCore.DisplayManagement (For the manifest)
* OrchardCore.ResourceManagement (Managing Bundle Dependencies)
* OrchardCore.ContentManagement (Razor Extensions)

## Add Template
Now, I'm going to customize the downloaded theme into a template and add it to the project. As stated before, I'm going to apply the template using razor pages instead of Liquid.

I'm going to create the following folders to the project:
* ./Controllers
* ./Views
* ./Views/Shared
* ./Views/Home
* ./wwwroot

And add some "default" classes:
* Controllers/HomeController.cs
* Views/Home/Index.cshtml


The content is irrelevant for now, but keep in mind that Layout.cshtml is the most important file and, as it is in ASP.NET MVC, will have the template page of the site and the wwwroot will have the static files content.

Now I need to add a new file at the root of Views folder called **_ViewImports.cshtml** with the following content:
```csharp
@*
    For more information on enabling MVC for empty projects, visit http://go.microsoft.com/fwlink/?LinkID=397860
*@

@inherits OrchardCore.DisplayManagement.Razor.RazorPage<TModel>
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper *, OrchardCore.DisplayManagement
@addTagHelper *, OrchardCore.ResourceManagement
@addTagHelper *, OrchardCore.Menu
```
This content will assist us with the razor views and prevent us from replicating imports through the views.


So now, I'll copy the folders **assets**, **css** and **js** from the downloaded template and pasted into the  **wwwroot** folder, and copy the index.html into the Views folder as **Layout.cshtml**.

Now, I have to edit the **Layout.cshtml** and fix the references for the static assets of the page. The changes made are related to:

* css 
* js 
* images
* etc

To reference the files I need to switch from:
* **href="css/...** to **href="~/[ProjectName]/css/...**
* **src="assets/...** to **src="~/[ProjectName]/assets/...**
* **src="js/...** to **src="~/[ProjectName]/js/...**

Afterward, I'll make to more changes to get information from Orchard and exposing it in the template:
**Culture Language**
```html
<html lang="@Orchard.CultureName()">
```

**Site name**
```html
<title>@RenderTitleSegments(Site.SiteName, "before")</title>
```

That is about it.


## Before Starting the Application
Before I start the application there are a few things to do first. I hope you believe in me, when I say we have a functional Theme/Template site but by the time the template is added into the Theme gallery it will present an empty preview of the theme layout. To overcome that issue I added a image into the root of the **wwwroot** folder called **Theme.png**:
![Orchard Core Theme preview][orchardcore.theme.03]

One last thing, I need to go to the **OrchardCoreCMS project and reference this theme project, so the CMS can add it to the themes section.



## Clean, Build and Start the Application
So let start the application and fill the setup page:
![Orchard Core setup][orchardcore.theme.04]

I have chosen the "Agency" recipe just because it has similar sections than the template that I adapted, but at this point it is irrelevant to the recipe. I can add it to any recipe.

![Orchard Core Recipe Home Page][orchardcore.theme.05]
As I enter I can see the default "Agency" theme. To change the theme I have to go to the admin page: To access the admin page, just type "https://localhost:5001/admin".

![Orchard Core Login][orchardcore.theme.06]
I fill the login data and then follow the steps to reach the Theme catalog, by accessing: Design -> Themes

![Orchard Core Login][orchardcore.theme.07]

![Orchard Core Login][orchardcore.theme.08]

![Orchard Core Login][orchardcore.theme.09]

As you can see, the template is shown in the catalog with the preview image. Without the **Theme.png** file, the theme would be shown as the "Safe Mode Theme".

Now I'm going to click "Enable" and the "Make Default":
![Orchard Core Login][orchardcore.theme.10]
![Orchard Core Login][orchardcore.theme.11]

By now when I access the site, I will see:
![Orchard Core Login][orchardcore.theme.12]

Now, I go back to the theme project (remember that the project is a razor template) and will change the "START BOOTSTRAP" at the menu bar and change it to the Site Name. To do that I just have to replace the "START BOOTSTRAP" for "@Site.SiteName". Hit save, refresh the page and ....

Tcharan.... 
![Orchard Core Login][orchardcore.theme.13]



# Knowledge Consolidation
Navigate throw the template, extract the layout and apply some partial views. See what are the Default Orchard Core properties that you can use, what they mean and how can be applied. Explore the Razor extension methods, they are pretty cool :)

Now this is something to keep in mind, if the theme has some orchardcore content types dependencies to show information into the site, simply creating a theme isn't enough. Generic themes are available, while using OrchardCore buildin properties, otherwise you are customizing for a purpose such as the Agency Recipes, or any other.

# Conclusion
In the next article, I explained how we can develop a custom Orchard Core Template. This was pretty simple, but it covers most of theming. There are a few things extra that we can apply in theming such as the ResourceManifest. But for now we will ignore it.





# Resources
* Official site: https://www.orchardcore.net
* Orchard Doje by Lombiq: https://orcharddojo.net/orchard-resources/CoreLibrary/
* Lombiq Youtube Channel: https://www.youtube.com/channel/UCDVUxCz2RvkgTbA0wAYKwRA
* Orchard CMS net Youtube Channel: https://www.youtube.com/channel/UCOPLovO0E8kfliE5bF9Y2Yg
* Orchard Skills Youtube Channel: https://www.youtube.com/channel/UCTwti4IV-uER89I39lTln0Q
* Visual Studio for Mac: https://visualstudio.microsoft.com/vs/mac/
* Jetbrains Rider: https://www.jetbrains.com/rider/
* Start Bootstrap: https://startbootstrap.com
* Liquid Templates: https://shopify.github.io/liquid/
* Static files in ASP.NET Core: https://docs.microsoft.com/en-us/aspnet/core/fundamentals/static-files?view=aspnetcore-3.1
* OrchardCore Razor Extensions: https://docs.orchardcore.net/en/dev/docs/reference/core/Razor/




[orchardcore.theme.01]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/Freelance.Theme.Project.png "Orchard Core Theme"
[orchardcore.theme.02]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCore.Theme.Empty.png "Orchard Core Empty Theme"
[orchardcore.theme.03]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/Theme.png "Orchard Core Theme Preview"
[orchardcore.theme.04]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.01.png "Orchard Core Theme Preview"
[orchardcore.theme.05]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.02.png "Orchard Core Theme Preview"
[orchardcore.theme.06]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.03.png "Orchard Core Theme Preview"
[orchardcore.theme.07]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.04.png "Orchard Core Theme Preview"
[orchardcore.theme.08]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.05.png "Orchard Core Theme Preview"
[orchardcore.theme.09]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.06.png "Orchard Core Theme Preview"
[orchardcore.theme.10]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.07.png "Orchard Core Theme Preview"
[orchardcore.theme.11]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.08.png "Orchard Core Theme Preview"
[orchardcore.theme.12]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.09.png "Orchard Core Theme Preview"
[orchardcore.theme.13]: https://webfilesstorageblog.blob.core.windows.net/cdn/blog/20200620/img/OrchardCoreTheme.Freelancer.10.png "Orchard Core Theme Preview"