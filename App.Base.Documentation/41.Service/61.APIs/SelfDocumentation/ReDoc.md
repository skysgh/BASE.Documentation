## Synopsis ##

ReDoc is another solution to render OpenAPI JSON files into Documentation, much as SwaggerUI does.


## Background ##

The free ReDoc UI is arguably prettier than the Swagger UI - but

- provides less configuration
- by default can only show one OpenAPI doc per url (unlike Swagger's dropdown option)




## Development Perspective ##

## Using Redoc ##

Similar to how one uses
```cs`
app.UseSwaggerUI(SwaggerUIConfigurer.Configure);
```
one invokes Redoc as follows:
```cs
app.UseReDoc( ReDocUIConfigurer.Configure);
```

which hands off to:

```cs
       public static void Configure(ReDocOptions c)
        {
            c.DocumentTitle = "Bugger...";

            //c.InjectStylesheet("/redoc/ui/customstylesheet.css");
            //c.IndexStream = () =>
            //{
            //    var r =
            //    System.Reflection.Assembly.GetExecutingAssembly()
            //            .GetManifestResourceStream
            //   ("Spikes.AspNetCore.ODataRouting.Embedded.Swagger.index.html");
            //    return r;
            //};

            //Ensure starts with slash:
            c.SpecUrl = $"{AppAPIConstants.SwaggerJSonRoot}/{AppAPIConstants.BaseRESTAPIsID}/{AppAPIConstants.SwaggerFileName}";
            //Several configuration options available:
            //c.ConfigObject.DisableSearch = true;
            //etc.
            // where     public const string ReDocRoot = "docs/api/redoc";
            c.RoutePrefix = AppAPIConstants.ReDocRoot;
        }
```

The key thing to note is that c.SpecUrl is not a method but a property.
Which means you can only assign one openAPI at a time. 

Which brings us to the next point - how to render multiple documents - below.

### Multiple Documents ###

The Multiple Document is a big deal.

So far I've only found the following link address it...and on top of it, it looks suspect from what I believe as to how UseRedoc works.

https://stackoverflow.com/questions/64057699/how-can-i-have-dropdown-for-different-api-version-in-redoc-c-sharp

