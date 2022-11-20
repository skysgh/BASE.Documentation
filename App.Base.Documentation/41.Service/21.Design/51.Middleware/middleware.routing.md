
## Background ##

* `[SubDomain.?][Domain]/[ResourceController]/[Action]/[IDs]?[ResourceModifiers]`
* Questions:
  * Resource Language
  * Section
  * Zoom
  * Layers
But the following is Not part of the Resource:
  * Context:
    * Tenancy:
    * UICulture:
    * Culture:
  *
  


## Development Perspective ##

* Whereas `UseRouting` determines what will be called, 
  `UseExtensions` actually executes it (leaves customisation space in between).
* By default `UseRouting` and `UseEndpoints` wrap whatever is specified in `Program.cs`.
  However, the order in which they are invoked can be changed by calling these methods *explicitly*.
  * This is required in scenarios such as the following:
    * rewritting the incoming request before it gets to `UseRouting`  

* Thereare two kinds of routing in ASP.NET Core:
  * Convention based
  * Attribute based
  * Most scenarios are Mixed between the two.

* `MapGet` is used to define an endpoint (a routetemplate + a delegate).
  * `app.MapGet("/hello/{name:alpha}", (string name) => $"Hello {name}!");`
* `MapControllers` maps whole Controllers
* `MapControllerRoute` is used to create a single route.


* Urls can be restrained by combining one or more restraints:
  * `{xyz:required}`
  * `{foo:bool|int|guid|datetime}`
  * `{age:required:min(18):max(65):range(18,65)}`
  * `{name:required:minlength(2):maxlength(16):length(2,16)}`


  * Start with:
    * `app.UseHttpsRedirection();`
    * `app.UseStaticFiles();`
    * `app.UseRouting();`
  *  Complete with:

    app.MapControllerRoute(
        name: "default",
        pattern: "{controller}/{action=Index}/{id?}");

    app.MapFallbackToFile("index.html");

* Finish off with:
  * `app.MapHealthChecks("/sysinfo/health");`//after adding checks earlier.
  * `app.MapControllerRoute(name: "default", pattern: "{controller}/{action=Index}/{id?}");`
  * `app.MapFallbackToFile("index.html");`
