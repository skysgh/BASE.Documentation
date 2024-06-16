# Synopsis #



# Background #
To faciliate uptate, APIs benefit from:

- being easily available: 
  - to authenticated users (always preferable to keep a minimum of auditability as to who is accessing a system)
- documented
  - providing a machine readable description of the APIs so that clients can consume it to auto-generate service client
    - potentially even provide such a service, generating service client source code from the above machine-readable 
      description, so they don't have to install any tooling (one less step required)
  - provde an sdk containing
    - a client developed around a copy of the above generated api service client code
    - unit tests invoking the api direction, or the client
  

Developing APIs from working annotated code, has advantages over defining a specification first:
- advantages:
  - it is based on working needs versus designs that are often more onerous
  - provides a testable service to back the documentation


## Process ##

The process of exposing Documentation is performed in two key steps:

* Develop the machine-readable OpenAPI JSON/YAML
* Automatically develop a UI based on the JSON file.

## Developing the OpenAPI File ##


### Dependencies ###

The required nuget package is `Swashbuckle.AspNetCore`

### The Objective is to  ###

Develop an output json file. 

So you first need a constant to define where it will be:

```cs
public static AppAPIConstants {
    //Must not end with slash (so also must not be '/')
    //And if no start slash, will be relative to above docs:
    //but if slash included, will be relative to root:
    //And in general, probably best to stick to something generic.
    //so...?
    public const string SwaggerJSonRoot = "/openAPI";
    ...
    public const string OpenAPIFileName ="openapi.json";
}
```
These are then used to configure where to build files:
```cs 
    var app = builder.Build();

    //then:
    app.UseSwagger(c=>
    {
        //NOTE: First {} is dynamic
        //      Second is template token
        //      ...not the same thing...
        c.RouteTemplate = $"{AppAPIConstants.SwaggerJSonRoot}/{{documentname}}/{OpenAPIFileName}";
    });
```


## Controlling what gets included in the OpenAPI description file

See:

- https://github.com/domaindrivendev/Swashbuckle.AspNetCore#generate-multiple-swagger-documents



## Hiding Endpoints ##

See:

https://www.thecodebuzz.com/swagger-show-hide-api-action-method-openapi/