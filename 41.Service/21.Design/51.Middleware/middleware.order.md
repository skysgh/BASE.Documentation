## Development Perspective ##

* `UseStaticFiles` goes before `UseRouting`.
* A (presumably custom) middleware can only use `context.GetEndpoint();`
  to get info as to what Controller & Action was used *after*
  `UseRouting` has been invoked, which fills it.
* `UseExtensions` (which actually invokes the action comes *after* `UseRouting`
  * Whereas `UseRouting` determines what will be called, 
    `UseExtensions` actually executes it (leaves customisation space in between).
*  Authentication and Authorization goes between `UseRouting` and `UseEndpoints`
* Url Manipulation (changing) has to be done *before* `UseRouting`



* "Apps typically don't need to call UseRouting or UseEndpoints. 
  WebApplicationBuilder configures a middleware pipeline that wraps 
  middleware added in Program.cs with UseRouting and UseEndpoints. 
  For more information, see Routing in ASP.NET Core."

* Order:
  * app.UseHsts();
  * app.UseHttpsRedirection();
  * custom url manipulation
  * UseStatic
  * UseRouting
  * custom controller/action/parameters logger
  * UseExtensions

