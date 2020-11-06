Service Registration can get really tedious. You have an interface definition, with one or more implementations. Maybe you need to register them in your Module or Application or perhaps you're using Shiny and need to register it in your Startup.

The Magician uses a pattern similar to MEF in which a Registration attribute is added to the service implementation. There is an attribute matching each of Prism 8's registration APIs. For example let's say that we had the following services:

```csharp
public interface IBackendOptions
{
    string Uri { get; }
    string ClientId { get; }
}

public interface IApiClient
{
    // your API here...
}
```

We know that we'll be using these services in our app, typically we might have something like:

```csharp
protected override void RegisterTypes(IContainerRegistry containerRegistry)
{
    containerRegistry.RegisterSingleton<IBackendOptions, BackendOptions>();
    containerRegistry.Register<IApiClient, MyApiClient>();
}
```

While you can continue to do that with services manually, with the Magician you can now instead decorate your service implementations like:

```csharp
[RegisterSingleton]
public class BackendOptions : IBackendOptions
{
}

[Register]
public class MyApiClient : IApiClient
{
}
```

As you might expect, the Magician will generate the code that you would have written above. This however helps to ensure that as you are working on your service implementation you will always be able to see at a glance that it is registered which additionally helps as other developers on your team work on the code later on.

!!! note
    Scoped Services are currently not supported. If you have a good use case for why you really need a Scoped Service you should discuss this in Discord, and it could be added. At the current time, it's my belief that only in the case of the Prism INavigationService in Prism.Forms is there truly a good case for Scoped services in a Prism application.

## Multiple Interfaces

There are times in which you may find yourself implementing multiple services. As example we may implement our IApiClient, and IDisposable on MyApiClient. We have no real way of knowing however whether you want MyApiClient to be registered for IApiClient or IDisposable. In this particular case you can specify the service type like:

```csharp
[Register(typeof(IApiClient))]
public class MyApiClient : IApiClient, IDisposable
{
    // your code here
}
```

## Registering Multiple Services for a Single Implementation

Sometimes you may find a need to have multiple smaller service definitions. As an example let's say that we're building an modular application. We might have an Authentication Module with an Authentication Service that handles getting the authentication token we will use to work with our API, and ultimately invalidating our session / token when the user signs out. We may however want to expose the Sign Out functionality in a common interface that can be shared between various modules in our application. In this case we might end up with something like:

```csharp
public interface IUserSessionService
{
    Task SignOutAsync();
}

public interface IAuthenticationService
{
    Task<string> SignInAsync();
}

public class AuthenticationService : IAuthenticationService, IUserSessionService
{
    // your code here
}
```

In this case we can provide the AuthenticationService with the RegisterMany or RegisterManySingleton attribute depending on whether we expect a new instance or a single instance of the Authentication Service to be maintained.

```csharp
[RegisterManySingleton]
public class AuthenticationService : IAuthenticationService, IUserSessionService
{
    // your code here
}
```

!!! note
    When using RegisterManySingleton you will get a single instance of the Service implementation that will be used across all services. It will not be a single instance per service.

!!! note
    If you have an interface such as IDisposable that should not be implemented be sure to pass the service types in the constructor of the attribute