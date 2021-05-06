Blazor WebAssemly with SignalR
==============================

> Tutorial URL: [Tutorial @ Microsoft.com](https://docs.microsoft.com/en-gb/aspnet/core/tutorials/signalr-blazor?view=aspnetcore-5.0)

## Setup

After project creation, just alike a Xamarin.Forms project, the template is made of separate projects.

- BlazorWebAssemblySignalRApp.__Client__
- BlazorWebAssemblySignalRApp.__Server__
- BlazorWebAssemblySignalRApp.__Shared__

### Client side

In the client we need to install the SignalR client NuGet package `Microsoft.AspNetCore.SignalR.Client`.

We modify `Pages\Index.razor`. 
There is little to no explanation on what it does. I will try to interpret the code and cannot garantee its exactitude for now.
This part was actually made after the server side operations were completed.

```csharp
@using Microsoft.AspNetCore.SignalR.Client
```
Because we rely on SignalR and installed a package, we need to explicitely use it with the `@using` directive. Just as I did on the TodoList project because I did not use the default location for the classes.

`@inject NavigationManager NavigationManager` is not explained. 
```csharp
@implements IAsyncDisposable
```
The parallel with class definition is obvious.
Checked the directive to use in place of __extends__ and it seems to be `@inherits`. There is an overridden method in the code part though (details a bit further).

The rendering code is straight forward. Not much to note.

```csharp
private HubConnection hubConnection;

// ... anything here ...

protected override async Task OnInitializedAsync()
{
   hubConnection = new HubConnectionBuilder()
      .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
      .WithAutomaticReconnect()
      .Build();

   hubConnection.On<string, string>("ReceiveMessage", (user, message) =>
   {
      var encodedMsg = $"{user}: {message}";
      messages.Add(encodedMsg);
      StateHasChanged();
   });

   await hubConnection.StartAsync();
}
```
According to documentation `HubConnection` type is used to invoke hub methods on SignalR server. Its instance must be created with an `HubConnectionBuilder`. The later can be chained methods to configure its behaviour or set it up. `WithUrl()` takes some more code in parameters to finally set the URI of the HubConnection.
`WithAutomaticReconnect()` was not part of the example code but was needed when used with Chrome which disconnects and is unable to reconnect after it has put the related tab into sleep mode.
`Build()` returns our instance as it should be in a builder design pattern.

```csharp
hubConnection.On<string, string>("ReceiveMessage", (user, message) =>
{
   var encodedMsg = $"{user}: {message}";
   messages.Add(encodedMsg);
   StateHasChanged();
});
```
SignalR uses a WebSocket to maintain a connection between clients and servers. It rely on a messaging system. The above code is a message handler for the `ReceiveMessage` message. It is coming along with parameters which are in turn user to format a response line which will be added to the `messages` collection.
`StateHasChanged()` notifies the component state has changed (who would have guessed) and triggers a rerender of the page. It should be called automatically on `EventCallBack` structures. Here we need an explicit call.

```csharp
await hubConnection.StartAsync();
```

Actually starts the connection to listen to hub method calls.

```csharp
async Task Send() =>
        await hubConnection.SendAsync("SendMessage", userInput, messageInput);
```

This is the method we call upon clicking on the relevant button. Takes the bound fields to form a message and sends it. 

```csharp
public bool IsConnected =>
        hubConnection.State == HubConnectionState.Connected;
```

Returns the connection state by querying the currently used `hubConnection` instance state property which is of type `HubConnectionState`. This allows to return a boolean instead so the code is cleaner.

### Server side

In the server we create a SignalR hub in a newly created `Hubs` folder.
It inherits the `Hub` class.

In Startup.cs we use that new namespace for the need of setting up SignalR.

> In ConfigureServices

```csharp
    services.AddSignalR();
    // ... Anything here ...
    services.AddResponseCompression(opts =>
    {
        opts.MimeTypes = ResponseCompressionDefaults.MimeTypes.Concat(
            new[] { "application/octet-stream" });
    });
```

> In Configure

```csharp
    app.UseResponseCompression();

    // ... Anything here ...

    app.UseEndpoints(endpoints =>
    {
        // ... Anything here ...
        endpoints.MapHub<ChatHub>("/chathub");
        // ... Anything here ...
    });

    // ... Anything here ...
```
