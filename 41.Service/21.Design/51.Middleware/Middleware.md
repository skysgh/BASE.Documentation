


## Development Perspective ##

* Invoke involves the development of two things:

  * The middleware itself, which is
    * A class
      * with any name (no conventions requird), 
      * that doesn't implement any interface.
    * with a `Constructor`:
      * requiring the injection of an `HttpContext`
      * and any other services if needed
    * Methods:
      * A single method called `Invoke` with the following signature: ``
      * The method is for your handling
      * And finishes with invoking `await next(context)` if it is a non-terminating event.
      * note that one can write something below the `next` statement if you need to do something 
        after all the nested handlers (the next...) have done their thing. 
* An extension method to apply it to the `IApplicationBuilder`
  * Following the convention of naming the Extension Method with one of the following 3 prefixes, depending on intent:
    * `Use...` (if it is a *Terminating* Middleware (ie,doesn't call the `next` handler))
    * `Add...` (if is is a ***non**-Terminating* Middleware (ie finishes up by invoking `next` handler))
    * `Map...`

That's it.

What you notice from the above is:

* As the extension method is to apply it to an ApplicationBuilder
  it is after the `IApplicationProvider` has been built from the 
  `IServiceContainer`, so its perfectly acceptable for the constructor
  to have arguments as to whatever services it needs to do its job.
* If you need to know ControllerName/ActionName (eg: for logging)
  must be injected into pipeine *after* UseRouting.


## Resources ##
* https://elanderson.net/2019/12/log-requests-and-responses-in-asp-net-core-3/
