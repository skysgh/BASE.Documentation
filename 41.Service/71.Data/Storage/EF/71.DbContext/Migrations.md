# Synopsis #

Migrations are the practically the most powerful aspect of EF - yet often not used to their full potential.


# Background #


# Misconceptions #

You'll often see Seeding being done in a DbContext's OnModelCreating. And it seems highly logical that it means that the changes will be implemented.
But you'll notice that it doesn't have a means to Save Changes(). That's a hint. 

What's going on the OnModelCreating is actually *describing* the Model -- Seed material included -- stopping just short of actually making the Model/Tables/Seeding. 

That gets done when you've made changes to the model and save a Migration.

## Setup: Assemblies ##

There are reasons to take the time to create Migrations in their own folder. If you think about it, the Migrations are *not* part of your app, for starters.
Plus, some

## Setup: Dependencies ##
The assembly that contains the app's DbContext requires:

```cs
Microsoft.EntityFrameworkCore.Design
```


## First Commands 
One uses command line tools to make changes to the code base, by developing new migration.cs files/classes.


```ps
dotnet ef migrations add --help

Options:
  -o|--output-dir <PATH>                 The directory to put files in. Paths are relative to the project directory. Defaults to "Migrations".
  --json                                 Show JSON output. Use with --prefix-output to parse programatically.
  -n|--namespace <NAMESPACE>             The namespace to use. Matches the directory by default.
  -c|--context <DBCONTEXT>               The DbContext to use.
  -p|--project <PROJECT>                 The project to use. Defaults to the current working directory.
  -s|--startup-project <PROJECT>         The startup project to use. Defaults to the current working directory.
  --framework <FRAMEWORK>                The target framework. Defaults to the first one in the project.
  --configuration <CONFIGURATION>        The configuration to use.
  --runtime <RUNTIME_IDENTIFIER>         The runtime to use.
  --msbuildprojectextensionspath <PATH>  The MSBuild project extensions path. Defaults to "obj".
  --no-build                             Don't build the project. Intended to be used when the build is up-to-date.
  -h|--help                              Show help information
  -v|--verbose                           Show verbose output.
  --no-color                             Don't colorize output.
  --prefix-output                        Prefix output with level.
  ```

So

```ps
// Case senstive flags btw:
// where flags are put after command
// where `InitialCreate` is the first migration, and you provide new names as you see fit:
dotnet ef migrations add InitialCreate --startup-project "App.Base.Host" --project "App.Base.Infrastructure" --output-dir "Data\Storage\Db\EF\Migrations" 
```


