## Synopsis ##




## Development Perspective ##



## Resources ##


* https://stackoverflow.com/questions/49340627/net-core-limit-plugin-permissions
  * Notes:
    * "we do not offer sandboxing features in .NET Core."
    * "Use os provided security boundaries, such as user accounts for running processes with the least set of privileges"
* https://askto.pro/question/how-to-limit-actions-of-the-loaded-assembly
  * A way of securing a dll is use Reflection to inspect the Assembly at load time.
    * For example:
      * If it is Requiring anything else than Appication & Shared, do not permit it.
      * If it is invoking any type under certain system namespaces (eg: System.IO), then dump.
  * Use ICSharpCode.Decompiler (ILSpy);
    * "new FileStream"
    * "new FileReader"
    * "File.Open"


* Frameworks to study:
  * https://natemcmaster.com/blog/2018/07/25/netcore-plugins/
  * https://www.infoq.com/articles/Plugin-Framework-DotNet/