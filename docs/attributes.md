The Magician works through a combination of understanding the context of your application (are you using WPF, Xamarin.Forms, Uno Platform, Shiny Lib, Microsoft.Extensions.DependencyInjection, etc), and attribute composition. Included in the Magician package is a lightweight class library the only contains some helper attributes. These help you to write code that is clean but clear on it's intent.


## AutoInitialize Attribute

The AutoInitialize attribute can be used on any ViewModel to initialize properties that have been passed via the Navigation or Dialog Parameters. This is placed on the ViewModel class.

When the required initialization method is already present in the ViewModel an AutoInitialize method will be generated and then called via IL Weaving.

- On ALL dialog ViewModels - OnDialogOpened
- On Prism.Forms - implements IInitialize.Initialize
- On Wpf/Uno - implements INavigationAware.OnNavigatedTo

## AutoInitializeParameter Attribute

This is a complimentary attribute that can be used in conjunction with the AutoInitialize attribute. By default all public properties within a class with the AutoInitialize attribute will be initialized by looking in the parameters for a matching key. The AutoInitializeParameter attribute should be used when you need to either override the default parameter name or mark a property as being required.

```csharp
public partial class SomeViewModel : BindableBase
{
    [AutoInitializeParameter("vehicle")]
    public Vehicle Model { get; set; }
}
```

Given the sample code we would expect that by default in the parameters we would be looking for a parameter with the key `Model`. The search would be case invariant. By adding this attribute with the value `vehicle`, we now explicitly expect the parameter key to be `vehicle`.

## AutoRegisterViews Attribute

You may have a lot of views that need to be registered, and while it isn't hard code, it is tedious to add the registrations for every View/ViewModel. We also know that while Prism looks for