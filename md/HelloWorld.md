 Hello World
============

As usual, technologies follow the philosophy of introducing themselves with an *Hello World* application.
The prerequisite is __.NET 5 SDK__ being properly installed. In other words the `dotnet` command shall provide a valid answer.
For instance:
```
C:\>dotnet

Usage: dotnet [options]
Usage: dotnet [path-to-application]

Options:
  -h|--help         Display help.
  --info            Display .NET information.
  --list-sdks       Display the installed SDKs.
  --list-runtimes   Display the installed runtimes.

path-to-application:
  The path to an application .dll file to execute.
```

## Creating the project stub

Just like other popular Web JS frameworks such as Angular, Vue or React, we can create a project stub via command line. 
The aforementionned `dotnet` command is used in that matter.

`dotnet new blazorserver -o BlazorApp --no-https`

The tutorial does not provide this info and the following is just deduction and may be wrong to any extent.
- `new`: means we create a new project stub.
- `blazorserver`: is the type of project. We could also have WebAssembly projects. Could it also take `webassembly` instead?
- `-o BlazorApp`: indicates the name of the new folder to be created.
- `--no-https`: Is it deactivating secure HTTP for the sake of this tutorial?

## Project stub structure
We are briefly presented with the content that has been generated:
- `BlazorApp.csproj` defines the app project and its dependencies.
- `Program.cs` is the entry point for the app that starts the server.
- `Startup.cs` is where you configure the app services and middleware.
- `App.razor` is the root component for the app.
- `The BlazorApp/Pages` directory contains some example web pages for the app.

## Running the application
Simply run the command `dotnet watch run`. 
As usual in CLI applications, it can be halted with <kbd>Ctrl</kbd>+<kbd>C</kbd>.

The Razor Pages have a `.razor` file extension. At first glance they look close to ASP pages<sup>1</sup>. They also seem to be inspired by
the philosophy behind JS frameworks. We are explained they are made of components just as in Angular or React. The Component name
seem to be taken from the `.razor` file name. 
For instance `Counter.razor` makes a `<Counter />` component available to use.

Just like in ASP pages/views the Razor Pages can hold HTML.

On the top of the page is an annotation that seem to be dealing with the routing among the site:
- `Index.razor` has `@page "/"` at its top.
- `Counter.razor` has `@page "/counter"` at its top.


In the context of Blazor they can also contain C# code.
Variables and special attributes must be preceded by the `@` symbol.

<small>
- 1 : I actually mostly worked with Razor views with both ASP .NET MVC and ASP .NET Core. 
Thought they are very close to each other, they seem to hold some differences depending on the root technology used.
Should it be ASP.NET MVC/Core or Blazor.
</small>

>

> Sample from the Counter.razor source.
```html
<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

In the above example, we introduce the `currentCount` integer variable in the view.
On the next line we make use of the `@onclick` event attribute to call a method called `IncrementCount`

Both the variable and method are defined in the block of code at the bottom of the page.

> Sample from the Counter.razor source
```csharp
@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

We therefore have both the presentation and underlying logic in the same document.

A simple practical work make us modify the Counter component so that it can accept a parameter to modify the incrementation step
of the counter.

> In the Counter.razor source.
```csharp
@code {
    private int currentCount = 0;

    [Parameter]
    public int IncrementAmount { get; set; } = 1;

    private void IncrementCount()
    {
        currentCount += IncrementAmount;
    }
}
```
We created a property `public int IncrementAmount { get; set; } = 1;` and gave it the default value 1.
Should there be no parameter provided to a Counter component, that value will be used.

The `[Parameter]` annotation above
that property identify it as a possible parameter taken by the component.

> In the Index.razor source.
```html
<Counter IncrementAmount="10" />
```

Where ever we would need it, we can add that Counter component and provide that optional `IncrementAmount` parameter with
a custom integer value.
