Blazor WebAssemly with SignalR
==============================

> Tutorial URL: [Tutorial @ Microsoft.com](https://docs.microsoft.com/en-gb/aspnet/core/tutorials/signalr-blazor?view=aspnetcore-5.0)

## Setup

After project creation, just alike a Xamarin.Forms project, the template is made of separate projects.

- BlazorWebAssemblySignalRApp.__Client__
- BlazorWebAssemblySignalRApp.__Server__
- BlazorWebAssemblySignalRApp.__Shared__

## Client setup

In the client we need to install the SignalR client NuGet package `Microsoft.AspNetCore.SignalR.Client`.

## Server setup

In the server we create a SignalR hub in a newly created `Hubs` folder.
It inherits the `Hub` class.