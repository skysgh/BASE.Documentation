# Synopsis #

A base solution 
* should not limit itself to a single db provider,



# Development Perspective #


# Constraints #

As usual, having to configure things in `IServiceCollection` does limit options.


## How to Build DbContext subclasses ##


### What Not to Do ###

```cs 
public class ApplicationDbContext : DbContext
{
    string _connectionString;
    DbContextOptionsBuilder optionsBuilder;

    public ApplicationDbContext(){
        this(@"Server=(localdb)\mssqllocaldb;Database=Test");
    }
    
    //Don't ever do it this way:
    public ApplicationDbContext(string connectionString)
    {
        _connectionString = connectionString;
    }

    public ApplicationDbContext(DbContextOptionsBuilder optionsBuilder)
    {
        _optionsBuilder = optionsBuilder;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        _optionsBuilder = optionsBuilder;
        // What not to do is embed in the DbContext subclass a connection string.
        // cause you can't change either the connection string, or Provider used:
        _optionsBuilder.UseSqlServer(_connectionString);
    }
}
```

Not much, but slightly better is going back to the `ConfigureServices` phase 
and try the following:

```cs
public void ConfigureServices(IServiceCollection services)
{
    ...
    services.AddDbContext<ApplicationDbContext>(
        //wow: can read oncifugration data directly.
        //But should you use it?
        // Probably not because of its simplicity:
        // - it's hard coding a reference to SqlServer, versus any other provider.
        // - does not permit dynamic connectionstring development later (Tenancies...)
        options => options.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
}
```
The above probably registers a factory around `ApplicationDbContext`, injecting in the above `options`
which is injected into the following default constructor.

But you can see one problem already...
```cs
//How do you dinamically choose different dbProviders:
//(o => o.UseInMemoryDatabase("MyDatabase"));
// OR:
 //o.UseSqlServer("name=ConnectionStrings:DefaultConnection"));
 // OR:
 // .UseSqlite(_connection)  //":memory:"
 //etc..


```

```cs
public class ApplicationDbContext : DbContext
{
    // truly hate the convoluted approach, but
    // must accept a DbContext type specific DbContextOption<> 
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

Alternatively, (and its a little riskier as you have have to handle Disposal later for sure) 
one can take over the whole process and:

```cs
//Instead of registering a DbContext, one can register a DbContextFacory
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContextFactory<ApplicationDbContext>(
        options =>
            options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
}
```
which can later be used:
```cs
void SomeControllerConstructor(IDbContextFactory<ApplicationDbContext> contextFactory){
    _contextFactory.CreateDbContext();
}
```