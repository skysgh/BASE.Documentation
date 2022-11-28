# Synopsis #

Routing is more or less the essence of web servers: they route requests to code.
ASP.NET Core's default position is to lock in the list of possible routes at startup, 
which doesn't help much with enabling late registration of routes required by plugins.





# Development Perspective #

## Who Selects Controllers ##

As per 

https://learn.microsoft.com/en-us/aspnet/web-api/overview/web-api-routing-and-actions/routing-and-action-selection

Controller selection is handled by the `IHttpControllerSelector.SelectController` method. This method takes an `HttpRequestMessage` 
and relies on an implementation of `IHttpControllerTypeResolver` to find controllers types that implement `IHttpControllerTypeResolver`, are public, not abstract, and end with the word `Controller`.

Which explains, btw, how OData maps them too. I think.


## Who creates Controllers? ##

* An instance of `IControllerActivator`.
* The default is `DefaultControllerActivator` which is an interesting beast who sort of half-uses DI (it uses `TypeActivatorCache`) .
* You can switch to `ServiceBasedControllerActivator` if you use the `AddControllersAsService()` extension method.
  * Note that it adds all controllers as `Transient` (versus `Singleton`) just in case.

You can replace it with what ever you create:

```cs
builder.Services.Add(ServiceDescriptor.Transient<IControllerActivator, MyServiceBasedControllerActivator>());
```

See: https://andrewlock.net/controller-activation-and-dependency-injection-in-asp-net-core-mvc/



Basics of OData Routing:
* https://learn.microsoft.com/en-us/odata/webapi/built-in-routing-conventions

Background:

* OData uses Routes as well.
* the abstract `EndpointDataSource` is what handles route registration - it's just hidden under ExtensionMethods (`MapControllers`, etc.)
* `ĪEndpointRouteBuilder` exposes a `Collection` of `EndpointDataSource`s called `DataSources`
* `app` implements `ĪEndpointRouteBuilder`, therefore has a property called `DataSources`
* The extension methods add to this Collection. 
* An `EndpointDataSource` has a 
  * *readonly* list of `Endpoint` objects and 
  * a `IChangeToken` to reset it if updated (we'll use it later) 

* Can make one's one implementation of the *abstract* `EndpointDataSource`
* which one either
  * add directly `app.DataSources.Add(new MyEndpointDataSource())` 
  * wrap as an extension method of `IEndpointRouteBuilder` objects.
  * wrap as an extension method, that retrieves the MyEndpointDataSource via DI (`app.DataSources.Add(app.Services.Get<MyEndpointDataSource>))` (crappy code, but you get the point that we're creating a dependency on a singleton we registered earlier)

Next is to load the List of Endpoints with one or more endpoints created as follows:

*   `CreateEndpoint("/myEndpoint", async context =>  context.Response.WriteAsync("Hello World!");)`
invoking

```cs
   private static Endpoint CreateEndpoint(string pattern, RequestDelegate requestDelegate) =>
        new RouteEndpointBuilder(
            requestDelegate: requestDelegate,
            routePattern: RoutePatternFactory.Parse(pattern),
            order: 0)
        .Build();
}
```

## Dynamic Route Translations ##

See `DynamicRouteValueTransformer` as per:
* https://medium.com/@fedonman/dynamic-controller-routing-in-asp-net-5-ac81a6bb8a95


# Resources #

Essential reading includes:
  * https://mariusgundersen.net/dynamic-endpoint-routing/
  * But note that this requires the routes being added to a ServiceCollection