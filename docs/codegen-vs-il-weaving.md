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

