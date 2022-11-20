TODO

## Creating Module ##

* In Visual Studio:
  * Right Click on the *.sln
  * Select `Open Folder in File Explorer`
* In File Explorer:
  * Copy the `Module01` Module Directory
  * rename the `Module01 - Copy` Directory to an appropriate name: eg: `Module32`
    * Take your time choosing the number carefully, as it is difficult to change later.
	  Hint, choose an appropriate number within the following range:
	  * 00-09: System
	  * 11-19: Common Business Modules (CRM, etc.)
	  * 21-29: Specific Organisation Modules (specific to this Organisation)
	  * 31-79: Specific Business Division within this Organisation.
* In the directory of the new Module:
  * Right-Click the `prepare-project.ps1`
  * Select Edit in `Visual Studio Code`
* In Visual Studio Code (after configuring it to run PowerShell, always a worthy step to do on a dev station):
  * Select `Start Debugging` to load the file into memory.
  * Use "Ctrl-`" to open the CLI.
  * Type `Prepare-Project` to run the script:
    * When prompted, enter a new Module Name. eg: `App.ModuleXX`. This will:
	  * rename directories starting with `App.Module01` to `App.ModuleXX`
	  * rename the files starting with `App.Module01` to `App.ModuleXX...`
	  * Within `*.cs`, `*.csproj`, `*.cshtml`, `*.md`, `*.config` `*.txt` files
	    * replace all occurances of `App.Module01` with `App.ModuleXX` (eg: web.config)
		* replace all occurances of `AppModule01` with `AppModuleXX` (eg: in namespaces, using, interfaces, classnames, etc.)
  * Tip: recognize that it's a hack powershell...and it still sometimes leaves a folder 
    un-renamed (file locks?). So be happy that most of the work is done, but be 
	accepting to keep an eye out for having to do a final manual pass for one file or folder that gets missed 
	(and don't worry, the will be error messsages to guide you to finding which file was skipped). 
  * TODO: the Files within the new Module are renamed. But the unique Project Guids (defined in the *.csproj) 
    are still duplicates of Module01. So far we have seen no issue due to this (it appears the Guids 
	are automatically updated by Visual Studio in the next step).
* In visual Studio:
  * Create a new Solution Folder: `ModuleXX`
  * In the following specific order, add the new *.csproj files to the new Solution Folder:
    * App.ModuleXX.Shared
	* App.ModuleXX.Domain
	* App.ModuleXX.Infrastructure
	* App.ModuleXX.Application
  * Via Find and Replace, perform a find on the Entire Solution for `Module01` (case-insensitive) and change any references to Module01 to ModuleXX only for files that are located in the ModuleXX folders.
  * Update the References on each project. They will be pointing to the Core Assemblies (Good), but they will also still be pointing to App.Module01 assemblies (not good). Update them as follows:
    * App.ModuleXX.Shared
	  * App.Core.Shared (no change)
	* App.ModuleXX.Domain
	  * App.Core.Shared (no change)
	  * App.Core.Domain (no change)
	  * App.Module01.Shared (remove)
	  * App.Module01.Domain (remove)
	  * App.ModuleXX.Shared (add)
	  * App.ModuleXX.Domain (itself)
	* App.ModuleXX.Infrastructure
	  * App.Core.Shared (no change)
	  * App.Core.Infrastructure (no change)
	  * App.Module01.Shared (remove)
	  * App.Module01.Infrastructure (remove)
	  * App.ModuleXX.Shared (add)
	  * App.ModuleXX.Infrastructure (itself)
	* App.ModuleXX.Application
	  * App.Core.Shared (no change)
	  * App.Core.Application (no change)
	  * App.Core.Infrastructure (no change)
	  * App.Module01.Shared (remove)
	  * App.Module01.Domain (remove)
	  * App.Module01.Infrastructure (remove)
	  * App.ModuleXX.Shared (add)
	  * App.ModuleXX.Domain (add)
	  * App.ModuleXX.Infrastructure (add)
	* Tips: we've found that I had a better success rate by actually removing the References marked with (no change), closing the dialog
	  reopening the dialog, adding the References back in. Maybe this is when Visual Studio rejigs the Project Guid. 
  * Clean the Solution. Before continuing, it's important to clean out all the Bin/Obj folders our you'll have confusing 'ghost' dlls that
    will cause compilation errors...or passes that cause run errors. Just prepare a fresh clear work space before going any further.
  * Build the new Projects, one at a time, in a specific order, working through bugs one at a time.
    * App.ModuleXX.Shared
	* App.ModuleXX.Domain
	* App.ModuleXX.Infrastructure (usually the most tricky, but you'll get there...)
	* App.ModuleXX.Application
  * In App.Host, reference  three of the four new assemblies (the domain assembly can be excluded).
	  This will ensure that when the solution is built, the dlls are copied to the Host's bin.
    * Explanation: if not done they will at best end up in the App.ModuleXX.Application bin, so build will say it was successful, 
	  but run will fail due to reflection not finding them in the host bin.
  * There is absolutely no use trying to run yet. 
    * Do the following Pre-Run checks:
      * All Modules have the same Class names. But different Namespaces. The Fully Qualified Name (ie, the Namespace + ClassName)
	    makes them unique from each other, so that reflection can find them in clumps of modules. 
	  * The only file that is named differently per Module is:
	    * App.Module.Shared.IAppModuleXX <- just eyeball it to ensure the number is equal to the module name.
      * The only string that is named differently per Module is:
	    * App.ModuleXX.Infrastructure.Constants.Module.Names.ModuleKey = "ModuleXX" <-- just eyeball it to ensure it too is unique.
	    * Note that this string must be unique per module as it ends up being used to setup
	      * the Db Schema (see App.ModuleXX.Infrastructure/AppModuleXXDatabaseInitializer)
		  * the OData root path (see App.ModuleXX.Application).
	  * The config file is where the Code First DbInitializer is defined. Add the following section, with the correct Module number inserted where needed:

      <context type="App.ModuleXX.Infrastructure.Db.Context.AppModuleXXDbContext, App.ModuleXX.Infrastructure">
        <databaseInitializer type="App.ModuleXX.Infrastructure.Db.Context.AppModuleXXDatabaseInitializer, App.ModuleXX.Infrastructure"/>
      </context>
    * Remove all EF migrations from ModuleXX that may have been copied across from Module01, from the folder .\App.ModuleXX.Infrastructure\Initialization\Db\Migrations\Default

  * Build. 



After renaming a Module

App.ModuleXX.Application/Constants/Api/ApiControllerName.cs
* Add Controller Names.

Need to hook App.Host to App.ModuleXX.Application

* Need a Clean (as App.Host bin has copies of older dlls referencing each other differently. Have to clear out, or you'll have ghosts).

