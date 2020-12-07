Ok so we know that the Magician works through a combination of Code Generation, IL Weaving, and providing Analyzers and Code Actions purpose built for Prism developers. But how exactly do you use the Magician?

While there are certain things that the Magician may be able to infer about your intentions like you have a declared interface, but haven't implemented one or more methods, the Magician will often try to provide a default implementation for you. However most things that get generated for you will require you to let the Magician know that you want to do something. The Magician includes a very small library of Attributes that can be used throughout your application. These attributes help the Magician understand what your goals are.

```csharp
[ViewModelBase]
public partial class ViewModelBase
{
}
```

You probably will want a good ViewModelBase included in your application. The Magician will generate a customized ViewModelBase class for you and may infer certain things about your project while generating it. In the sample above we have provided the Magician the ViewModelBase attribute, but we haven't given it any properties or a base type. When generating the ViewModelBase it will evaluate our project to determine:

- Do we have a Forms, Wpf, or Uno project
- Do we have a reference to System.Reactive, ReactiveUI, or Shiny Lib

In the event that we have a reference to ReactiveUI we will get a base class that inherits from ReactiveObject a base ViewModel that follows a more Reactive style of programming. However if we don't then it will instead use Prism's BindableBase.

## Next Steps

- [Auto Initialization](code-gen/auto-initialization.md)
- [Automatic View Registration](code-gen/view-registration.md)
- [Dialogs](code-gen/dialogs.md)
- [Service Registration](code-gen/service-registration.md)
- [View Bindable/Dependency Props](code-gen/view-bindable-props.md)
- [ViewModelBase](code-gen/viewmodelbase.md)