## About ##

The use of an Inversion of Control (IoC) intelligent Factory
to manage Dependency Inversion (DI) -- especially heavily 
relying on Reflection for Discovery -- is atypical.

It also makes a system's Startup sequence appear more like
magic. 

Which in turn makes debugging just a bit harder.

This doc is to explain what's going on a bit better.

## Process ##

`App.Host.Web` is invoked by IIS which invokes its `Main()`

In Main, the general steps occur, but one particular one
is the swapping of the default `IServiceProvider` based 
IoC service for the more powerful one provided by 
the `Lamar` library.

# Startup Sequence
- Main() is in Program.cs of App.Host.Web
- A WebApplication is created first, and Services added.
- Note that Services need to be registered before .Build() is invoked.
- Note that .Map-ing is called after .Build.

