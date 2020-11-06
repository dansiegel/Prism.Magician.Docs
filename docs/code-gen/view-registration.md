Automatic View Registration couldn't be easier and has some distinct performance advantages over the built in Prism Automatic View registration for Prism.Forms. The Magician's Automatic View Registration:

- works on ALL platforms including WPF & Uno Platform
- bypasses runtime reflection with compile time generated code
- registers both the View & ViewModel
- extends the standard Prism ViewModelLocator defaults to find ViewModels with other common conventions

Sounds great right? But how do you use it? Simply add the AutoRegisterViews attribute to your Module, PrismApplication, or PrismBootstrapper (WPF only) as shown below:

```csharp
[AutoRegisterViews]
public partial class App : PrismApplication
{
    // your code here
}
```

## ViewModel Location

By Default Prism's ViewModel Locator convention looks for the ViewModel in the ViewModels namespace with a ViewModel suffix like:

- ViewA -> ViewAViewModel
- SomeView -> SomeViewModel

The Magician is a little smarter where we will also look for

- SomePage -> SomeViewModel

In addition to that the Magician will check in both the Views and ViewModel's namespaces incase you prefer to keep your ViewModel and View next to each other in a more logical construct.

## What is a View

This is actually a little more complex than you might think when it comes to automatic View discovery for Navigation. In the case of WPF or Uno platform, and in the event that you are using Regions for Prism.Forms your View is realistically derived from a type that could make it a custom or composite control. As a filter the Magician will evaluate all appropriate types within the Dialogs, Views, and Pages namespaces.

!!! note
    The Magician will not register any View types that are abstract, and will automatically register the Xamarin.Forms TabbedPage & NavigationPage

## Customized View Naming

Let's assume that you have a View name like `UserProfilePage`. There are 4 common conventions that developers tend to use.

- UserProfilePage
- UserProfile
- userProfilePage
- userProfile

Depending on your preference, in your application project add the NameFormatProvider attribute and pass in the preferred Style. If you do not do this your application will continue to use the default Type name.

```csharp
[assembly: NameFormatProvider(NameFormatProviderStyle.CamelCaseNoPageSuffix)]
```

## Region, Dialog, & Navigation Keys

As the Magician works to register each View it finds for Page Navigation, Region Navigation or as a Dialog, it will collect a mapping of what each name should be along with each type. When it's all done you will get up to 3 new classes generated in the .Navigation namespace of your project.

```csharp
public partial class NavigationKeys
{
    public const string HomePage = "home";
    public const string LoginPage = "login";
}

public partial class DialogKeys
{
    public const string AlertDialog = "alert";
}
```

With a generated keys class you can now directly reference what you need in an MVVM friendly way without violating the MVVM pattern with:

```csharp
_regionManager.RequestNavigate(nameof(ViewA));
```

Additionally the Magician will evaluate your XAML and construct a list of the various Regions within your WPF, Uno Platform, or Xamarin.Forms application, and will create an additional class like:

```csharp
public partial class RegionNames
{
    public const string ContentRegion = "ContentRegion";
}
```