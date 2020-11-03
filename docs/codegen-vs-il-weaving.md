# Code Generation vs IL Weaving

This is perhaps an old debate of which is better Code Generation or IL Weaving? To be blunt, they are both great when used in the areas that they perform best.

To start let's take something that you're probably already pretty familiar with:

```csharp
public partial class ViewA
{
    public ViewA()
    {
        InitializeComponent();
    }
}
```

You see this in every code behind class for your XAML Views, `InitializeComponent();`... but what is it and where did it come from? Simply put the InitializeComponent method is generated for you at build and handles loading the XAML View or the conversion between XAML and C# code. This is much the same as when you may provide an Element in XAML an `x:Name="someName"` and then can access it via the generated `someName` property in your code behind. During design time builds it may appear that this is broken as the tasks to generate the code have not run or made the generated code available for intellisense.

Now that we understand what Code Generation what exactly is IL Weaving? IL Weaving is a process which changes the assembly IL commands post compilation. Continuing with the above example let's say that we had something like:

```csharp
public partial class ViewA
{
    public ViewA()
    {
        ViewModelLocator.SetAutowireViewModel(this, true);
    }
}
```

In this case we don't have a reference to InitializeComponent which will be generated, but we have our own code in place. With IL Weaving we are able to maintain a code base that looks like the above while achieving the following:

```csharp
public partial class ViewA
{
    public ViewA()
    {
        InitializeComponent();
        ViewModelLocator.SetAutowireViewModel(this, true);
    }
}
```

## Benefits & Drawbacks

There is a clear benefit here that our code can remain nice and clean as the InitializeComponent can be added into our existing code for us. However when debugging the debugger isn't going to be able to stop on the method invocation. On the flip side if we had a debug breakpoint in InitializeComponent, as generated code we could actively set breakpoints and step through our normal debugging process.

## How The Magician Uses these concepts

The Magician primarily focuses on updating getters and setters for your properties while relying on Code Generation as much as possible. This means that you may have code like:

```csharp
[Bindable]
public string Message { get; set; }
```

Which the Magician will then turn into:

```csharp
private string _message;
public string Message
{
    get => _message;
    set => SetProperty(ref _message, value);
}
```

In this sample we use Code Generation to add the missing backing field while using IL Weaving to update the getter and setter.
