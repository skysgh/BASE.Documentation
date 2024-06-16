## Development Perspective ##

* Use the `UseRequestLocalization` middleware
  to determine the culture. 
* By default, it checks 
  * query string
  * query
  * header 
* So have to slip in a custom one to check the 
  second section of a url, and insert it in the 
  above list in first place
* See: `UrlRequestCultureProvider`
* See: `app.UseRequestLocalization`
 

## Resources ##
* https://www.codeproject.com/Articles/5324504/Localization-in-ASP-NET-Core-Web-API
* https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.requestlocalizationoptions?view=aspnetcore-6.0