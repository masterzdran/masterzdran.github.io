---
layout: post
title: Blazing Blazor - Component lifecycle.
date: 2019-12-17 14:00:00 +0100
description: What is the lifecycle of the components?.
img: Blazing_Blazor.png
cover: Blazing_Blazor_Part_III-cover.png
tags: [Blazor, Netcore, Web Development, Education, Software]
---

In this post I'll investigate the component lifecycle, has we well know the importance of knowing the lifecycle of anything in technology :).

From the [official documentation](https://docs.microsoft.com/en-us/aspnet/core/blazor/lifecycle?view=aspnetcore-3.1)
> The Blazor framework includes synchronous and asynchronous lifecycle methods. Override lifecycle methods to perform additional operations on components during component initialization and rendering.

# First of all ...

... I'll reuse a component so I can see what is the component lifecycle flow.

So by editing the **Counter.razor** and overriding all (relevant) methods:
**void OnAfterRender(bool firstRender)**
```
protected override void OnAfterRender(bool firstRender)
{
    Console.WriteLine("OnAfterRender");
}
```
**void OnInitialized()**
```
protected override void OnInitialized()
{
    Console.WriteLine("OnInitialized");
}
```
**void OnParametersSet()**
```
protected override void OnParametersSet()
{
    Console.WriteLine("OnParametersSet");
}
```
**Task OnAfterRenderAsync(bool firstRender)**
```
protected override Task OnAfterRenderAsync(bool firstRender)
{
    Console.WriteLine("OnAfterRenderAsync");
    return base.OnAfterRenderAsync(firstRender);
}
```
**Task OnInitializedAsync()**
```
protected override Task OnInitializedAsync()
{
    Console.WriteLine("OnInitializedAsync");
    return base.OnInitializedAsync();
}
```
**Task OnParametersSetAsync()**
```
protected override Task OnParametersSetAsync()
{
    Console.WriteLine("OnParametersSetAsync");
    return base.OnParametersSetAsync();
}
```
**Task SetParametersAsync(ParameterView parameters)**
```
public override Task SetParametersAsync(ParameterView parameters)
{
    Console.WriteLine("SetParametersAsync");
    return base.SetParametersAsync(parameters);

}
```
**bool ShouldRender()**
```
protected override bool ShouldRender()
{
    Console.WriteLine("ShouldRender");
    return base.ShouldRender();
}
```

The flow of calls in the lifecycle is:
1. SetParametersAsync
1. OnInitialized
1. OnInitializedAsync
1. OnParametersSet
1. OnParametersSetAsync
1. OnAfterRender
1. OnAfterRenderAsync

And this sequence is executed everytime the component is called.

## SetParametersAsync
Without getting in to deep, this method is the first method invoked and sets the value of each property of the ParameterView (Parameter or CascadingParameter).

Small note:
> Parameters that don't have a corresponding value in ParameterView are left unchanged.

Once the parameters are set, then the initialize methods are invoked.

## OnInitialized || OnInitializedAsync
On these methods, the code that initiate the components are executed.

Small note:
> To perform an asynchronous operation, use OnInitializedAsync and the await keyword.

After initialization the OnParameterSet is invoked.

## OnParametersSet || OnParametersSetAsync
These methods are executed after component initialization and each time new parameter values are specified.

Small note:
> Asynchronous work when applying parameters and property values must occur during the **OnParametersSetAsync** lifecycle event.


## OnAfterRender || OnAfterRenderAsync
These methods are invoked when the component is rendered. In this stage is possible to perform adicional actions on the rendered component. 
The boolean parameter is set as true once component is rendered.
> Can be used to ensure that initialization work is only performed once.

```
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

Small note:
> Asynchronous work immediately after rendering must occur during the OnAfterRenderAsync lifecycle event.
>
> Even if you return a Task from OnAfterRenderAsync, the framework doesn't schedule a further render cycle for your component once that task completes. This is to avoid an infinite render loop. It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.

# Other Methods
Another method available to be overriden is **ShouldRender** method:
```
protected override bool ShouldRender();
```

Within this method it is possible to suppress UI refreshing. To do so the implementation return **false**.

Small note:
> Even if ShouldRender is overridden, the component is always initially rendered.

Blazor does it best to render all needed components at the right time, however in some cases it may need some help. In that case there is a method to be invoked:
```
protected void StateHasChanged ();
```
> Notifies the component that its state has changed. When applicable, this will cause the component to be re-rendered.

# Incomplete actions or Errors
All asynchronous are best effort and sometimes the actions performed may not be completed when the component is rendered. To prevent misplaced data or errors, consider to provide render logic in the component. For instace, in the **FetchData** component, until the forecast collection is fulfilled there is an render logic implemented:
```
@if (forecasts == null)
{
    <p><em>Loading...</em></p>
}
else
{
    /// the forecast table is shown.
}
```

# Conclusion
In this post I went through an overview of the components lifecycle, by identifing the flow and then going through each one of them.
By knowing the flow and its importance, the developer can think ahead and prevent some of the common pitfalls.

# Source Code
All the code can be reached at https://github.com/masterzdran/BlazorPlayground

# Resources
* [2019-12-17] https://docs.microsoft.com/en-us/aspnet/core/blazor/?view=aspnetcore-3.1