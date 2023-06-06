

## Developers Perspective ##

### Hookup ###

According to:
https://referbruv.com/blog/working-with-odata-integrating-an-existing-aspnet-core-3x-api/

You do

```cs
 services.AddControllers()
                .AddOData(opt => 
                    opt.AddRouteComponents("odata", EdmModelBuilder.Build()));
```

Except I can't resolve EdmModelBuilder for one.

After a lot of messing around, found:

https://docs.microsoft.com/en-us/odata/webapi/getting-started

* Install `Microsoft.AspNet.OData`


As for Routing:
https://devblogs.microsoft.com/odata/attribute-routing-in-asp-net-core-odata-8-0-rc/