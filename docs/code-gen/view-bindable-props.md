!!! important
    This feature is still in preview as the IL Weaving piece has not yet been completed

It's not at all uncommon when developing custom components to be working with the Application Framework's View types, and need to add a BindableProperty / DependencyProperty. These properties tend to be very verbose, not very type friendly, static, and a lot of typing. The Magician aims to make these tasks a little easier by allowing you to write convention based code that simplifies what your intent is. For this sample we'll look at a Xamarin.Forms Bindable Property, but WPF/Uno Platform Dependency Properties will work the same way.

Let's say that you're building a reusable UserProfileComponent and it will have a FirstName and LastName property which will bind to different labels. What your real intent here is that you have two properties, so why clutter it up?

```csharp
public partial class UserProfileComponent : ContentView
{
    [BindableProperty]
    public string FirstName { get; set; }

    [BindableProperty]
    public string LastName { get; set; }
}
```

!!! note
    When working with a WPF / Uno Platform project, use the DependencyProperty attribute instead. The BindableProperty attribute is ignored on those platforms and the DependencyProperty attribute is ignored in Xamarin.Forms projects.

!!! important
    Always be sure to make any class partial when using the Magician otherwise it will fail the build when it generates additional code.

The Magician will first CodeGen our BindableProperties like:

```csharp
public partial class UserProfileComponent : ContentView
{
    public static readonly BindableProperty FirstNameProperty =
        BindableProperty.Create("FirstName", typeof(string), typeof(UserProfileComponent), null);

    public static readonly BindableProperty FirstNameProperty =
        BindableProperty.Create("LastName", typeof(string), typeof(UserProfileComponent), null);
}
```

Next the Magician will use IL weaving to change our getter and setter to:

```csharp
public partial class UserProfileComponent : ContentView
{
    public string FirstName
    {
        get => (string)GetValue(FirstNameProperty);
        set => SetValue(FirstNameProperty, value);
    }
}
```

!!! warning
    The IL weaving feature is still not complete.

## Default Values

When adding the BindableProperty or DependencyProperty attribute you can pass a default value in the constructor of the attribute. This value will be used as the default value in the generated BindableProperty or DependencyProperty.

```csharp
[BindableProperty("Dan")]
public string FirstName { get; set; }

[BindableProperty("Siegel")]
public string LastName { get; set; }
```

## Handling events

Handling Changed events with Bindable or Dependency Properties can be quite infuriating as you're working with the least common denominator rather than working with a strongly typed API. For example you may normally write a handler like:

```csharp
private static void OnFirstNameChanged(BindableObject bindable, object oldValue, object newValue)
{
    // Your code here
}
```

With the Magician you have both flexibility and the ability to continue writing the same horrible method handlers, or you can write more intelligent handlers like:

```csharp
private static void OnFirstNameChanged(UserProfileComponent component, string oldValue, string newValue)
{
    // Your code here
}
```

Granted that's better but still not really ideal in my opinion, so the Magician has you covered as you can also write your handlers like:

```csharp
private void OnFirstNameChanged(string oldValue, string newValue)
{
    // Your code here
}
```

Or you can keep it really simple like:

```csharp
private void OnFirstNameChanged()
{
    // Your code here
}
```

### Customizing Event Handler Behavior

Let's say that you have a 3rd property for the DisplayName and this will need to be updated whenever either the FirstName or LastName are updated. We can easily achieve this with the Magician:

```csharp
public partial class UserProfileComponent : ContentView
{
    [BindableProperty]
    public string DisplayName { get; set; }

    [BindableProperty(onChanged: nameof(UpdateDisplayName))]
    public string FirstName { get; set; }

    [BindableProperty(onChanged: nameof(UpdateDisplayName))]
    public string LastName { get; set; }

    private void UpdateDisplayName()
    {
        string name = null;
        if (!string.IsNullOrEmpty(FirstName))
        {
            name = FirstName;

            if (!string.IsNullOrEmpty(LastName))
            {
                name += $" {LastName[0]}.";
            }
        }
        else if (!string.IsNullOrEmpty(LastName))
        {
            name = $"Mr. {LastName}";
        }

        DisplayName = name;
    }
}
```