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

## Why is a Combination of CodeGen & IL Weaving Better?

Many fans of CodeGen believe that is is superior and you never need IL Weaving. This is just patently false. Let's look at a common case. When working with an MVVM Architecture we rely heavily on INotifyPropertyChanged to notify the Binding engine that a value has changed and the View needs to update something. If we look at the full code that we need we will again see:

```csharp
private string _message;
public string Message
{
    get => _message;
    set => SetProperty(ref _message, value);
}
```

While this is indeed the code required, it is clunky and quickly creates code clutter making it harder to come back to the code and see what our real intent is. It's for this reason we want to use something that will modify the code we write so that we can write less and keep our code easier to read in full. So what does the CodeGen version of this look like?

```csharp
public partial class SomeViewModel : BindableBase
{
    [Bindable]
    private string _message;
}
```

This is indeed less to write, and while the attribute may help us decipher a little bit what is going on it's still a bit unclear since we're really expecting our XAML to be like:

```xml
<Label Text="{Binding Message}" />
```

Ultimately a private backing field is not at all part of our intent, it is part of an ends to our means. So how can this be better? Well, traditionally you might have used PropertyChanged.Fody in which you might have:

```csharp
public partial class SomeViewModel : BindableBase
{
    public string Message { get; set; }
}
```

This is much better now we at least know where our public Message property came from, but our backing field doesn't actually exist in anything we can work with. This is where the Magician really shines with a partnership between CodeGen and IL Weaving.

### How The Magician Uses these concepts

The Magician primarily focuses on updating getters and setters for your properties while relying on Code Generation as much as possible. This means that your code remains clean, yet readable and clear on it's intent.

```csharp
public partial class SomeViewModel : BindableBase
{
    [Bindable]
    public string Message { get; set; }
}
```

Remember our real intent is to have a public property called Message which will be bindable for our View to consume. With this the Magician will use CodeGen to provide us our backing field, and IL Weaving to update the getter and setter resulting in the full code that we need to bind our properties in the View.

```csharp
public partial class SomeViewModel : BindableBase
{
    private string _message;
    public string Message
    {
        get => _message;
        set => SetProperty(ref _message, value);
    }
}
```

!!! note
    For those using ReactiveUI with a base class of ReactiveObject, be sure to use the `Reactive` attribute.