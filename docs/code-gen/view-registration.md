Automatic View Registration couldn't be easier and has some distinct performance advantages over the built in Prism Automatic View registration for Prism.Forms. The Magician's Automatic View Registration:

- works on ALL platforms including WPF & Uno Platform
- bypasses runtime reflection with compile time generated code
- registers both the View & ViewModel
- extends the standard Prism ViewModelLocator defaults to find ViewModels with other common conventions

## ViewModel Location

By Default Prism's ViewModel Locator convention looks for the ViewModel in the ViewModels namespace with a ViewModel suffix like:

- ViewA -> ViewAViewModel
- SomeView -> SomeViewModel

The Magician is a little smarter where we will also look for

- SomePage -> SomeViewModel

In addition to that the Magician will check in both the Views and ViewModel's namespaces incase you prefer to keep your ViewModel and View next to eachother in a more logical construct.

## What is a View

This is actually a little more complex than you might think when it comes to automatic View discovery for Navigation. In the case of WPF or Uno platform, and in the event that you are using Regions for Prism.Forms your View is realistically dervived from a type that could make it a custom or composite control. As a result we only search within the Views and Pages namespaces.

! note
    The Magician will not register any View types that are abstract, and will automatically register the Xamarin.Forms TabbedPage & NavigationPage

## Customized View Naming

This feature is still somewhat in flux as some additional functionality is planned, and how the Magician accomplishes custom view naming will be changing.