## About ##


## Development Perspective ##

* Each Module gets its own DbContext
* With its own Schema Name associated to the Module Name
* Inherited from the Base ModuleDbContext
* Exposing only the DbSets the Module is about (eg: Student)
* And not the DbSets of the Base.

* Whether it needs to be backed by an Interface is still up for debate.


### Resources ###
* https://codewithmukesh.com/blog/modular-architecture-in-aspnet-core/#Controller_Registrations
* https://www.thinktecture.com/en/asp-net-core/modular-monolith/
* https://www.google.com/search?q=modular+asp.net+core&ei=XCUpY5nSIfuZjuMPi_ecuAs&ved=0ahUKEwiZ8N2yqaL6AhX7jGMGHYs7B7cQ4dUDCA4&uact=5&oq=modular+asp.net+core&gs_lcp=Cgxnd3Mtd2l6LXNlcnAQAzIFCAAQgAQyBggAEB4QFjIGCAAQHhAWMgYIABAeEBYyBggAEB4QFjIGCAAQHhAWMgYIABAeEBYyBggAEB4QFjIGCAAQHhAWMgYIABAeEBY6BQgAEJECOgsIABCABBCxAxCDAToLCC4QgAQQsQMQgwE6EQguEIAEELEDEIMBEMcBENEDOggIABCxAxCDAToICAAQgAQQsQM6BAgAEEM6EAguELEDEIMBEMcBENEDEEM6CwguEIAEELEDENQCOgsILhCxAxCDARDUAjoFCC4QgAQ6CwguEIAEEMcBEK8BOggIABCxAxCRAjoKCAAQsQMQyQMQQzoFCAAQkgM6BwgAEIAEEAo6CggAELEDEMkDEAo6BAgAEAo6EAguELEDEIMBEMcBEK8BEAo6BwgAELEDEAo6CggAELEDEIMBEAo6DgguEIAEELEDEIMBENQCOhEILhCABBCxAxCDARDHARCvAToLCAAQgAQQsQMQyQM6CAgAEB4QDxAWOgoIABAeEA8QFhAKSgQIQRgASgQIRhgAUABY7iNg-SRoBXABeAGAAbMCiAHgJpIBCDAuMTUuOC4xmAEAoAEBwAEB&sclient=gws-wiz-serp