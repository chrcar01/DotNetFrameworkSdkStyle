Example [SDK style](https://learn.microsoft.com/en-us/dotnet/core/project-sdk/overview) project that target old school MVC .NET Framework project
# Notes on the process
* start a new project, ASP.NET Web Application(.NET Framework) -> Mvc style, .NET Framework 4.8
* run it
* upgrade any packages that are out of date or have known vulnerabilities
* runt it and make sure everything still works

* upgrade-assistant tool: 
ripped from here: https://stackoverflow.com/a/77161020/1594171

* install it:
`dotnet tool install -g upgrade-assistant`

* run the tool on targets project
`upgrade-assistant upgrade -o feature.sdkstyle .\MvcSdkStyle.csproj`

* resulting project won't compile, doesn't know what old school mvc is...
* modify the csproj to use this sdk: MSBuild.SDK.SystemWeb/4.0.88
note: MSBuild Sdk nuget packages are different, this automagically downloads this nuget package
https://github.com/CZEMacLeod/MSBuild.SDK.SystemWeb
```
<Project Sdk="MSBuild.SDK.SystemWeb/4.0.88">
...
</Project>
```
* install 2 packages
* `Install-Package Microsoft.AspNet.Mvc`
* `Install-Package Microsoft.AspNet.Web.Optimization`

* change output type from exe to lib
```  
<Project Sdk="MSBuild.SDK.SystemWeb/4.0.88">
  <PropertyGroup>
    <OutputType>library</OutputType>
  </PropertyGroup>
</Project>
```
* update launchsettings.json to get IISExpress working:
```json
{
  "profiles": {
    "IISExpress": {
      "commandName": "Executable",
      "executablePath": "C:\\Program Files (x86)\\IIS Express\\iisexpress.exe",
      "commandLineArgs": "/path:\"$(SolutionDir)$(ProjectName)\" /port:50275 /systray:true",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

Runtime issues with DotNetCompilerPlatform assembly conflicts...
* Downgrade the compiler reference in the Web.config to 3.6, something kept pulling in the 3.6 reference
```
<configuration>
  <system.codedom>
    <compilers>
      <compiler language="c#;cs;csharp" extension=".cs" warningLevel="4"
                compilerOptions="/langversion:default /nowarn:1659;1699;1701"
                type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider,
                      Microsoft.CodeDom.Providers.DotNetCompilerPlatform,
                      Version=3.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" />
      </compilers>
  </system.codedom>
</configuration>
```
## At the end of the day... this was NOT worth the effort but it was a fun process


