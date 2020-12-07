ReactiveUI is an extremely popular library that further helps developers write MVVM applications using the Reactive Programming model. There are often questions about whether you should use ReactiveUI or __________. The simple fact of the matter is that when building a Prism Application, this is not an or, but rather an AND. ReactiveUI and Prism work very well together.

In these cases you may find yourself using ReactiveObject as a base class rather than Prism's BindableBase. The Magician fully understands how to use ReactiveUI.

!!! important
    Do NOT use ReactiveUI.Fody with the Magician. The Magician replaces all functionality from that weaver package, including Reactive properties, and ObservableAsHelper's.

## Reactive Properties

When working with normal Bindable properties using BindableBase we use the `Bindable` attribute on the properties we want to have the Magician update for us.

```csharp
public partial class SomeViewModel : BindableBase
{
    [Bindable]
    public string Message { get; set; }
}
```

However when using ReactiveObject as our base class the getter and setter are a little different and ultimately the Bindable paradigm is the wrong terminology. To keep this clear the Bindable attribute used on properties within a class inheriting from ReactiveObject will generate an error. You should instead use the `Reactive` attribute on these properties.

```csharp
public partial class SomeViewModel : ReactiveObject
{
    [Reactive]
    public string Message { get; set; }
}
```

## ObservableAsPropertyHelper

ReactiveUI has a fantastic helper type known as ObservableAsPropertyHelper<T\>. This helper class enables us to take at times complex Observables and return simple properties that we will use to bind to, while at the same time helping us to raise the Property Changed event. So what does this look like?

```csharp
public class ViewAViewModel : ReactiveObject
{
    public ViewAViewModel(IConnectivity connectivity)
    {
        connectivity.WhenInternetStatusChanged()
            .ToProperty(this, nameof(IsOnline), out _isOnlineHelper, true);
    }

    private ObservableAsPropertyHelper<bool> _isOnlineHelper;
    public bool IsOnline => _isOnlineHelper?.Value ?? false;
}
```

Here again the Magician uses a combination of things to help you out and keep you code clear on intent.

```csharp
public partial class ViewAViewModel : ReactiveObject
{
    public ViewAViewModel(IConnectivity connectivity)
    {
        connectivity.WhenInternetStatusChanged()
            .ToProperty(this, nameof(IsOnline), out _isOnlineHelper, true);
    }

    [ObservableAsProperty]
    public bool IsOnline { get; }
}
```

Ultimately the Magician has no clue what business logic you require to create the ObservableAsPropertyHelper, however we can again use CodeGeneration to create the private field and IL weaving to update the getter here.