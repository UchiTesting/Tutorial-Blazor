To do list
==========

> Tutorial URL: [ToDo List Tutorial @Microsoft.com](https://docs.microsoft.com/en-gb/aspnet/core/tutorials/build-a-blazor-app?view=aspnetcore-5.0)

## Creating a component

We can create extra components via command line:
`dotnet new razorcomponent -n Todo -o Pages`

Options:
- `-n Todo`: Name of the Razor component. Naming convention require a capitalized first letter.
- `-o Pages`: Specifies the component must be created in the `Pages` subfolder.

The output should look like this:
```
C:\>dotnet new razorcomponent -n Todo -o Pages
The template "Razor Component" was created successfully.
```

### Adding routing information

Adding the `@page "/todo"` annotation, we provided a new route to access the component.

## Updating the Menu

We use it to add it in the menu held in `Shared\NavMenu.razor` file.

## Implementing the Todo List

### We need a model

We create a class `TodoItem` to hold some information. Unlike what is asked. I decide to put the class in a new `Models` folder. Because of that I had to make sure to add a `@using Models;` instruction in the code part.

### Back to the component

Then we create the code to render a list of `TodoItem`, an input field and a button to actually add the item to the list. 

We create a `AddTodo()` method.

The button click event handler is given that method via the `@onclick="AddTodo"` attribute of the `<button>`.

To interact with the controls on the page, instead of directly reading the value in the input field, we will be using binding.
In the code we will create a field to be used for that binding.

```csharp
 private string newTodo;
```

Then the actual binding is done with the `@bind` attribute.

```html
<input placeholder="Something to do..." @bind="newTodo" />
```

The title gets modified to be dynamic and display the actual pending items.
```html
<h3>Todo (@todos.Count(todo => !todo.IsDone) pending items)</h3>
```
