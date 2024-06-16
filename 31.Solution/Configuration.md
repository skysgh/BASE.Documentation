

## Synopsis ##



## Development Perspective ##

### Build Settings ###

Solutions run within environments. 
So make sure to configure the environment first.

### Solution Settings ###

* Because there are TWO key projects that have to be started
  when clicking F5 (which is more or less F6 now, due to the 
  previously mentioned setting change...)
  * In Visual Studio
  * In the Solution Explorer
  * Right Click the Solution Node
  * Set Startup Projects...
  * Select Multiple Projects
  * Select both the Client and Service
  * Move the Service to first place, above Client
    so that it starts *first*.

### Check ###

* Service:
  * View Properties -> Debug -> General...
  * Click "Open Debug Launch Profile UI"
  * **Un**check the "Launch Browser when Debugging"
  * Record the App URL:
    * `https://localhost:7021;http://localhost:5021`
    * When there are more than one, record the part that is `https`:
      * `https://localhost:7021`
* 