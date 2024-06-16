# Synopsis #

Routing is *hard*. 
Routing for different modules by 3rd parties over which you have not control
is even *harder*.



## Issues ##


### Conflicts between OData and Web API.
The following text is highly insightful

*"api is the default prefix used by Web API controllers. You've mapped both the OData and API middleware to the same endpoint, so the same controller appears twice under the same endpoint, once for each middleware. ASP.NET Core can't decide which one to use. This matters because different filters are applied by each middleware. Web API doesn't recognize EnableQuery and would try to map the OData operators to parameters and fail. OData on the other had would generate a LINQ query out of them"*

Src: https://stackoverflow.com/a/72285035/19926885

