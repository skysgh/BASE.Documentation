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

