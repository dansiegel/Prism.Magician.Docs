# Magician Analyzers

Perhaps one of the most powerful things you never knew you needed, and one of my all time favorite stories. Have you ever seen a resume where someone claimed to be an expert at Intellisense? We all rely on Intellisense to help us write better code and refactor code that could be a little better. Without a doubt Roslyn has made this a much better process for .NET Developers.

The Magician ships with several Roslyn Analyzers to help power your Intellisense and light up some additional build warnings or errors to keep you on track. At this time the Analyzer suite is still in its infancy, and new analyzers and code refactoring will be introduced over time.

## Included Analyzers

- Application Lifecycle Analyzer
- Messaging Center Analyzer
- Dependency Service Analyzer
- NavigationResult Handling Analyzer
- Xamarin.Essentials Usage Analyzer
- Dependency Injection Anti-Pattern Analyzer
- Various Analyzers for improper use of Magician Attributes

## Planned Analyzers

- Unregistered Services
- Detect Additional Bad Coding Practices
- Detect Common Client Id's hard coded
- Detect Additional Bad Practices with Prism's Container
- Detect Invalid ViewModel names (i.e. ViewViewModel)
- Detect Direct INavigationService references when using the Generated ViewModelBase

## Will there be more?

Yes there will be more analyzers planned and implemented over time. If you have an issue that has caused you problems in the past, be sure to reach out in the Magician channel in Discord and let me know.