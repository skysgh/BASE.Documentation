# Synopsis #



# Programming Perspective #


It is poor design practice - for security, maintainability and availability reasons - to permit API endpoints to expose
system Entities (the entities the system uses for logic and/or ORM based storage). Best practice is to expose Data Transfer Objects (DTOs).

## Problem ##
In C#, a side effect is that entities need to be named something different than the system entities they represent - often using a suffix
such as 'Dto':

```cs
public class User {
  ...
} 
// and a related externally visible Dto:
public class UserDto {

}
```

When developed using a Convention based EDM model builder, the system creates a `$metadata` file that exposes this Dto name,
which in turn causes service clients to develop objects on their end that have the `Dto` suffix:

```xml
<edmx:Edmx xmlns:edmx="http://docs.oasis-open.org/odata/ns/edmx" Version="4.0">
  <edmx:DataServices>
    <Schema xmlns="http://docs.oasis-open.org/odata/ns/edm" Namespace="App.Base.Shared.Models">
       <EntityType Name="ParentDto">
         ...
         <NavigationProperty Name="Addresses" Type="Collection(App.Base.Shared.Models.Child)"/>
        </EntityType>
        <EntityType Name="ChildDto">
         ...
        </EntityType>
    </Schema>
    <Schema xmlns="http://docs.oasis-open.org/odata/ns/edm" Namespace="Default">
      <EntityContainer Name="Container">
        <EntitySet Name="exampleO1" EntityType="App.Base.Shared.Models.ParentDto"/>
      </EntityContainer>
    </Schema>
  </edmx:DataServices>
</edmx:Edmx>

```
Not a good look...

## DataContract and DataMember ##

The default serialisation can be overridden using `DataContract` and `DataMember`.

Note that this comes at a price: if you decorate a DTO with `DataContract` you *have* to decorate every property you want to go over the wire with `DataMember`, making it one more thing that is easy to forget and you have to check... 

```cs
[DataContract(Name ="Parent")]
    public class SomeBaseParentModel
    {

        [DataMember]
        public int Id { get; set; }

        [DataMember]
        public string Name { get; set; }

        [DataMember]
        public ICollection<SomeBaseChildModel> Addresses
        {
            get => _children;
            set => _children = value;
        }
        ICollection<SomeBaseChildModel> _children = new Collection<SomeBaseChildModel>();
    }
```

You can ignore things with `NotMappedAttribute`.

**Warning:**   
*If you are exposing System Entities (and why would you not take the time to use a DTO...?) DataContract/DataMember to control API serialisation will probably interfer with DB serialisation (ie what you ignore on the DTOs will cause EF to think you want it ignored from the DB as well...)*


## Convention ##

Is there a way to solve this by an EDM Convention?  I'm guessing there is, but I havn't looked into it first.

Something that would recognise whether entities and enums ens with a well-known suffix and if so strip it off before encoding the type definition.


## Fluent ##

When defining models by hand, one can give Properties a different name -- but have not yet found a way to rename Entities themselves.

```cs
var builder = new ODataConventionModelBuilder { Namespace = "Blah" };
var foo = builder.EntitySet<Foo>("Foo"); // Missing a Name property...
foo.Property(f => f.Bar).Name = "Jack";
foo.Property(f => f.Baz).Name = "Jane";
```

