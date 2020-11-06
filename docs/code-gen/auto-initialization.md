Auto Initialization was a powerful feature previewed in Prism.Forms 7.2. There were a number of limitations though as this was ultimately powered by runtime Reflection, and of course was limited to only supporting Xamarin.Forms applications.

## How The Magician Does it Better!

The Magician works at build time to generate the required Initialization and it doesn't care about whether you're working with a Forms, WPF, or Uno platform app.

```csharp
[AutoInitialize]
public partial class ViewAViewModel : BindableBase
{
    [Bindable]
    public string Message { get; set; }
}
```

Here we can keep our ViewModel nice and clean. We absolutely intend on having a Bindable Message property, but we don't need to add all of the boiler plate to check for it and set it. Instead we can simply add the AutoInitialize attribute to the ViewModel and the Magician will generate the initialization logic for us.

!!! note
    How the Magician operates here is a little different depending on whether we are working with a ViewModel for a Region, Dialog or Page Navigation.

In the event that you have already provided a method body for the Initialization method, the Magician will generate a AutoInitialize method. This method will then be invoked via IL Weaving from the correct initialization method.