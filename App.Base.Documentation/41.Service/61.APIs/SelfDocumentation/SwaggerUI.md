# Synposis #

The use of APIs benefits from them being

- being documented
- preferably automatically, so that one doesn't forget to do it,
- in a machine-readable manner, so that clients can automatically create service clients

For REST (and ODATA) based APIs this can be achieved by using Swagger - a library -
to
- document the APIs in a machine readable format (OpenAPI)
- and then build an interactive UI from the OpenAPI.

## Development Perspective ##

### Dependencies ###

- TODO/List

### Setup ###


### Tasks ###


#### Change the location of where OpenAPI JSON files are developed.

```cs
public static AppAPIConstants {
    //Must not end with slash (so also must not be '/')
    //And if no start slash, will be relative to above docs:
    //but if slash included, will be relative to root:
    //And in general, probably best to stick to something generic.
    //so...?
    public const string SwaggerJSonRoot = "/openAPI";
}
```
This is then used for configuration:
```cs 
    var app = builder.Build();
    //then:
    app.UseSwagger(c=>
    {
        //NOTE: First {} is dynamic
        //      Second is template token
        //      ...not the same thing...
        c.RouteTemplate = 
        $"{AppAPIConstants.SwaggerJSonRoot}/"+
        "{documentname}/swagger.json";
    });
```
But as you've moved it, you have to tell the UI part where to look for it:

```cs
app.UseSwaggerUI(SwaggerUIConfigurer.Configure);
/// which hands off to: 
public static void Configure(SwaggerUIOptions c)
{
    ...
    c.SwaggerEndpoint($"{AppAPIConstants.SwaggerJSonRoot}/{AppAPIConstants.BaseRESTAPIsID}/swagger.json", "Base Rest APIs");
    c.SwaggerEndpoint($"{AppAPIConstants.SwaggerJSonRoot}/{AppAPIConstants.BaseRESTAPIsID}/swagger.json", "Base Rest APIs");
    ...
}

```

### Change the URL of the Interface:
It's great that there documentation is being developed...
but at the root of the app is maybe not the cleanest location.
Best to put it somewhere else 

You need a constant first:
```cs
public static AppAPIConstants {
        //Must not end with slash (so also must not be '/')
        //And this one must not start with a slash either:
        //IMPORTANT: When you change this, you also have to change
        //matching string in "launchUrl": "docs/api/swagger" 
        //setting within launchsettings.json
        public const string SwaggerDocRoot = "docs/api/rest/swagger";
}
```
```cs
app.UseSwaggerUI(SwaggerUIConfigurer.Configure);
/// which hands off to: 
public static void Configure(SwaggerUIOptions c)
{
    c.RoutePrefix = AppAPIConstants.SwaggerDocRoot;
}
```

### Hiding the UI default display - Collapse Models section
The default UI is visually busy. For one, the Models are useful, but should be collapsed until needed:

```cs
    app.UseSwaggerUI(SwaggerUIConfigurer.Configure);
    // which hands off to:
    public static void Configure(SwaggerUIOptions c)
    {
        //c.EnableDeepLinking = true;
        //c.EnableFilter = true;
        //Hide the models (Important. Note the **plural** on 'model*s*'):
        // As per https://stackoverflow.com/questions/57138564/how-to-hide-the-models-section-in-swagger-ui
        c.DefaultModelsExpandDepth(0);
    }
```


### Update the UI Stylesheet

If you want, you can include your personal stylesheet (CSS won't take you far - but it's a start...).

You first have to create a wwwroot folder.

Then enable static file routing:

```cs
   app.UseStaticFiles();
```

Then create a style sheet in the wwwroot folder:
```css
.swagger-ui .topbar {
    background-color: #5772ad;
    border-bottom: 3px solid black;
}

```

Then reference it:
```cs
    app.UseSwaggerUI(SwaggerUIConfigurer.Configure);
    // which hands off to:
    public static void Configure(SwaggerUIOptions c)
    {
        c.InjectStylesheet("/swagger/ui/customstylesheet.css");
    }
```


### Update the Swggare UI Index Page

CSS won't take you far.

So the next thing is to develop a new INdex page.
But this time, it's not a static file, but an embedded resource.

You get the latest file from: TODO

And under it's properties, mark it as an Embedded Resource.

As is always the case of Embedded Resources, its' name is 'namespace' + 'folders' + 'filename'.

If you need to check you could check you got it right by:

```cs
    var check3 = System.Reflection.Assembly.GetExecutingAssembly().GetManifestResourceNames();
```
Anyway you wire the stream in as follows:
```cs
   c.IndexStream = () =>
    {
        var r =
        System.Reflection.Assembly.GetExecutingAssembly()
                .GetManifestResourceStream
        ("Spikes.AspNetCore.ODataRouting.Embedded.Swagger.index.html");
        return r;
    };
```

### Editing the UI Index Page

TODO


### Creating Multiple Documents

Only in the simplest POC is it ok to document all APIs in one file / automatically developed UI.

On the right side of the swaggerUI toolbar is a toolbar that shows the names of the available files. 
Which, by now, you understand is dynamically built from JSON files.

So you have to build multiple JSON files.

Each JSON file is created with:
- a unique ID that is usable as an URL, and
- a Title (something humanly readable).


As always, start with some constants:
```cs
public static class AppAPIConstants
{
    // Important: 
    // whereas the Title is human readable, the 
    // IDs have to be appropriate as URL segments:
    ...
    public const string BaseRESTAPIsID = "BaseRestV1";
    public const string BaseRESTAPIsTitle = "Base Standard REST APIs";
    // Next doc:
    public const string BaseODataAPIsID = "BaseODataV1";
    public const string BaseODataAPIsTitle = "Base OData APIs";
    ...
}
```
We've already seen the IDs be used when developing the JSON files:
```cs
c.SwaggerEndpoint($"{AppAPIConstants.SwaggerJSonRoot}/{AppAPIConstants.BaseRESTAPIsID}/swagger.json", "Base Rest APIs");
c.SwaggerEndpoint($"{AppAPIConstants.SwaggerJSonRoot}/{AppAPIConstants.PluginODataAPIsID}/swagger.json", "Base OData APIs");
```

But are also used to develop the docs themselves, which have a title:
```cs
public static void BuildSwaggerGenOptions(SwaggerGenOptions options)
{
...
configuration.SwaggerDoc(
    AppAPIConstants.BaseRESTAPIsID,
    new OpenApiInfo
    {

        Title = AppAPIConstants.BaseRESTAPIsTitle,
        Version = "v1",
        Description = "The App's standard REST APIs",
        ...
    }
...
}
```

### Assigning API Endpoints to different Docs

You then use the same IDs (not Titles) to assign the automatic documentation
to different openAPI docs:

```cs
[EnableQuery(PageSize = 100)]
[HttpGet(Name = "GetWeatherForecast")]
[ApiExplorerSettings(GroupName = AppAPIConstants.BaseRESTAPIsID)]
public IEnumerable<WeatherForecast> Get()
{
...
}
```


### Excluding Endpoints

Another thing that one may consider too busy for 
most API docs is the OData Metadata endpoint.

```cs
//As per https://stackoverflow.com/questions/70472622/how-to-hide-odata-metadata-controller-in-swagger
public class AppSwaggerODataControllerDocumentFilter : IDocumentFilter
{
    public void Apply(OpenApiDocument swaggerDoc, DocumentFilterContext context)
    {
        // Maybe depending on which doc?
        //if (context.DocumentName != openAPI doc){

        // remove controller
        foreach (ApiDescription apiDescription in context.ApiDescriptions)
        {
            var actionDescriptor = (ControllerActionDescriptor)apiDescription.ActionDescriptor;
            if (actionDescriptor.ControllerName == "Metadata")
            {
                swaggerDoc.Paths.Remove($"/{apiDescription.RelativePath}");
            }
        }

        // remove schemas
        foreach ((string key, _) in swaggerDoc.Components.Schemas)
        {
            if (key.Contains("Edm") || key.Contains("OData"))
            {
                swaggerDoc.Components.Schemas.Remove(key);
            }
        }
        //}
    }
}

```

which is registered as follows:

```cs
public static void BuildSwaggerGenOptions(SwaggerGenOptions options)
{
//As per
// https://stackoverflow.com/questions/70472622/how-to-hide-odata-metadata-controller-in-swagger
options.DocumentFilter<AppSwaggerODataControllerDocumentFilter>();
}
```

