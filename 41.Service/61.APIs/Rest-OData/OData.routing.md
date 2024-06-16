## Synopsis ##

Setting up OData routing is more difficult than it should be.
Compounded by poor documentation.


## Requirements ##

* Url Must disambiguated OData from other APIs protocols
  (eg: start with `api/odata` to disambiguate from `api/rest`,
  `api/soap`, etc.
* Url must contain version information to permit breaking
  changes while permitting existing clients to continue to use
  older versions for a while longer.
* Url may contain tenancy and culture information -- but that
  will have been parsed, used, and the url stripped by rewritting
  by an earlier middleware.
* Route of Controllers must contain information as ModuleName
  to permit multiple Controllers having the same name, as long
  as they are in different Modules (therefore namespaces).


## Developer Perspective ##

* Prior to OData v8 RC, OData routing relied on attributes
  that have been removed.
  * `ODataRoutePrefix` is one such.
* Documentation for v8 RC is available here:
* Routing is trickier than it should be get right.
  * Therefore highly recomment to use 
  `app.UseODataRouteDebug()` is enabled
  in order to be able to navigate to `~/$odata` to 
  see which routes have been classified as OData ones
  and not just API routes -- and of those, which ones
  are considered to be *Attribute* based, versus 
  *Conventions* based. 
* Although it's a bit more work, one will want 
    *Attribute* based in most cases because:
  * For trivial apps Convention based is fine enough, but
    for real apps one will quickly need the Attributes
    to disambiguate routes.
* But it seems that Convention based routing is the default
  * AND one can't turn it off,
  * and it's limited.
* On top of that, you'll always need Attributes of some kind
  whether you're working with Convention routing or Attribute
  routing.

### Common Attributes ###

*All* OData Controllers need to be decorated with `[ODataAttributeRouting]`
to define a Controller's actions as being OData endpoints.
If you don't, the Controller is treated as a generic API Controller
and the results don't come back wrapped in OData conventional json.

Note that one gets this attribute by default if you enherit from `ODataController`.

I'm also not sure at this point if there is any advantage inheriting from 
a generic API in order to have both? But assuming for now there is no 
advantage.

Have not figured out the point of `[ODataRouteComponent]` -- but it only
affects Convention based routing anyway, so no advantage to us?

### Model Building ###

Whether using Convention Routing or Attribute based Routing 
it all starts by developing a model


```cs
var builder = new ODataConventionModelBuilder();
//Access to SomeModel is via 
// some Controller called SomeController
// via some route similar to '~/api/odata/v{version}/'
builder.EntitySet<SomeModel>("Some");
...
var model = builder.GetEdmModel();
...
builder
  .Services
    .AddControllers()
      .AddOData(opt => opt.Count().Filter().Expand().Select().OrderBy().SetMaxTop(5)
      .AddRouteComponents(
          "api/odata/v{version}",
          model));
```

### Convention Based Routing ###
Note above that the `builder.EntitySet<SomeModel>("Some")`

a) is looking for a controller called `SomeController`.
b) won't accept anything more complicated (ie can't register something like `"SomePrefix/Some"`.

So although this Convention based approach is easy it has several issues, the most important of which are:

* at any point in time some developer could rename the controller
  and the app and break the route.
* the route prefix is insufficient to include a Module name.

Important: although the above route prefix (`api/odata/v{version}`) looks 
 like it would be applicable to all routes -- 
  but is only applicable when the route is Convention based
  (ie that the Controller name suffix matches the string used
  to register the EntitySet in the model)


### Attribute based ###

So have to go use other attributes. Namely

* Decorate the Controller with `[Route("api/odata/v{version}/Some")]` and
* the Acton with a `[HttpVerb("")] [HttpVerb("Get")]`.

What's not at first clear -- and theres very little clear documenation on -- are two key points:

* The Route has to start with the same Convention prefix used when 
  registering the model earlier.
* Now you can use any name (eg:`[Route("api/odata/v{version}/AnyName")]`)
  as long as you match it when registering model types (eg: `builder.EntitySet<SomeModel>("AnyName")`).
* But note that we're still stuck with being unable to prepend with module information (eg: `builder.EntitySet<SomeModel>("SomeModuleName/AnyName")`)

The most common ways you'll see routes defined are as follows:
```cs
// where a constant is used for "api/odata/v{version}/"
`[Route(ApiConstants.OdataApiRoutePrefix + "ModuleName/SomeUniqueModelName")]`
`[Route(ApiConstants.OdataApiRoutePrefix + "ModuleName/[controller]")]`
```

### Diagnosticating ###

* Because it's tricky, one really has to rely on 
  `app.UseODataRouteDebug` to have an `~/$odata` diagnostics path
  to investigate whether the route 'took' as an OData route 
  (ie the route is listed under the Odata controllers section, 
  and not the *Non*-OData controllers section).

### Further ###

If one can't prepend an slashed Module/Schema name in front of a Controller
name, one could use an underscore ("modulename_controllername"), but that
doesn't feel right (or wrong either?).

Another option may be to register multiple models.

