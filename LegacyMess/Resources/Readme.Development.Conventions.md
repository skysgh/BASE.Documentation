## About ##

Systems get complex. And therefore are hard, and costly to maintain and enhance.

A couple of ways to make it even more complex is to 
a) not follow DRY patterns so that there is one place to look
b) let every developer who touches the code base do it 'their way' so that each location
   is coded using a different approach.

So don't. Code using common conventions.

## Coding Standards ##
I don't really care about how many spaces you use to indent, whether you fold code at 80 characters
or leave spaces after every if. Code quality is not a typing standards exercise -- it's to ensure
that the code is as simple as it can be in order to be maintained for the least cost.

So the following rules are simple:

* Always enclose if/else/while statements in curly brackets. Don't *ever* skip them. 
  * If you don't know, look up the iPhone SSL bug that was caused by some developer 
    was too clever by half. And got it wrong. 
* Use Constants instead of using strings everywhere.
* Use Resharper. As a team agree on the common rules (eg '_' before private variables, etc.) and then stick to it. Not complicated.
* Use descriptive names. You know that 'x' stands for something. It adds time for someone else to figure out what you knew. 
* Comment complexity. Code should be simple. And therefore understandable. But trust me: none of us are that good. 
  So comment your code for developers you think are two levels down from you (eg, beginners).
* If you have long classes (eg: > 200 lines of code), you're probably not following SOLID principles. 
  Stop what you are doing and read up on SOLID. I'm serious. It's cheaper for us to pay for the time 
  it takes for you to get up to speed on SOLID than it is for the rest of the team to redo what you
  did write when you did not understand SOLID.

That's it. The rest is up to you.

## 
* CodeFirst:
  * ie, The database is *not* created using SQL, but created by EF ModelDefinitions.
* Migrations via APIs:
  * ie, any existing app data is not migrated using SQL scripts (ie, via the backend)
    but only via APIs, so that imported data is submitted to the same logic as any other
	operation.
	In other words, you don't import data from Db to Db -- data is exported, transformed to 
	json, which is then submitted to APIs as operations. Takes more setup time -- saves a lot
	on bugs later.
* 3rd Party Dependencies are Hell:
  * Keep dependencies on 3rd party nuget assemblies isolated to App.Core.Infrastructure and App.Core.Application
  * You may be frustrated at times, thinking you should be able to directly use a 3rd party library's classes
    (eg: EF's DbContext) from the assembly you are in (eg: App.ModuleX.Infrastructure, or App.ModuleX.Domain)
	but there will be a reason for keeping it inaccessible to your code. Depend on App.Core.Infrastructure -- not
	the 3rd party library. 

