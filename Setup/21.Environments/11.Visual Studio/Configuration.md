



## Tasks ##

The following tasks are required to set up the Development
Environment correctly.


### Git ###

Need to get Visual Studio to play nicely with Git, so that one
can work witht the GitHub repository within which the code is 
to stored and retrieved from.


## Angular ##

To install Angular globally:

`npm install -g @angular/cli`


## Build Settings ##

Because the relationships between Assemblies are 
  *intentionally* undefined (ie **DON'T** create References
  between Core and the late-loadable Modules!!!) 
  one has to teach Visual Studo to build All projects, 
  not just the ones in the chain of References descending
  from the Solution's Start project.

  Effectively making F6 the new F5:
  * In visual Studio
  * Open Tools
  * Open Options
  * Open Projects and Solutions
  * On Build and Run
  * **un**select *"Build on Startup projects and Dependencies"*
