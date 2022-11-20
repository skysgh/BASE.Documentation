# About #



Can't say it enough. Applications that manage information belonging to other people have the responsibility of being confidential and secure, at all times, from the first release.
* Security before Functionality.


## Requirements ##

* **NO** Environment Passwords should be embedded in any file, script, etc. in this repository.
  * Tip: remember that you can use files with *.ignore or *.secret at the end to ensure that files are not committed by mistake.

## Confidentiality ##
The basis of modern web security is ensuring that communication is over HTTPS rather than HTTP, in order to ensure that 
communication is confidential, before transfering tokens and other confidential information back and forth.

HTTPS is ensured at multiple levels. 
First of all `WebApiFilterConfig` applies the `RequireHttpsWebApiFilterAttribute` as a global Filter to all WebAPI calls.
This redirects all HTTP traffic to HTTPS trafific. 
Note that this can be enforced at the infrastructure level, but this Filter ensures basic aspect of security *in case*
Infrastructure Specialists do not configure this, or configure it incorrectly. By incorrectly, I mean it's poor practice
to disallow HTTP, but only provide a 404, rather than cause a redirect to the same page, but over HTTPS.




## Authorisation and Authentication: AOP versus Controller Filters ## 
A common strategy to ensure that all methods of all Controllers are secured by default (relaxed as needed), 
is to use AOC to Intercept the class creation, and wrap it with a proxy.
That is appropriate if the security is applied on the Application Service.
If the app logic is to run outside of a web host that might be applicable. But it adds complexity.
If not needed (as is the case in this app, as far as I can tell) then one can/should use Attribute based
Security, applied as Action Filters at the Controller/ApiController level.

The WebApiAppAuthorizeAttribute is applied to all controllers from within WebApiFilterConfig.

Note that the WebApiAppAuthorizeAttribute enherits from AuthorizationAttribute. Which enherits from AuthorizationFilterAttribute.
AuthorizationFilterAttribute is too raw for our needs, whereas AuthorizationAttribute adds a Role property that is useful. The only
downside is that by AuthorizationAttribute is using IsInRole. This is fine -- but in an OIDC based app, this requires us to enherit 
and override it to ensure its checking against Claims.

## Resources ##

* WebAPI Filters:
  * https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/authentication-filters
