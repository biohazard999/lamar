<!--Title: Lamar Diagnostics-->

Like StructureMap before it, one of Lamar's big differentiators from other IoC tools is its strong support for built in diagnostic tools.

The `Lamar.Diagnostics` Nuget library can be used to expose all of Lamar's diagnostic capabilities
to the command line of your .Net Core or .Net 5.0 systems. To get started, just add a Nuget dependency to `Lamar.Diagnostics` to your application. This package relies on the .Net command line integration from [Oakton](https://jasperfx.github.io), so you'll need to set up Oakton as shown in this sample `Program.Main()` method:

<[sample:using-lamar-diagnostics]>

Once the `Lamar.Diagnostics` Nuget is installed to your application and you've opted into Oakton to handle command line options, typing this command at the root of your project will show all the installed commands:

```
dotnet run -- help
```

If `Lamar.Diagnostics` is installed, you should see three lamar related commands as shown below:

```
  ---------------------------------------------------------------------------------------------
    Available commands:
  ---------------------------------------------------------------------------------------------
         check-env -> Execute all environment checks against the application
          describe -> Writes out a description of your running application...
              help -> list all the available commands
    lamar-scanning -> Runs Lamar's type scanning diagnostics
    lamar-services -> List all the registered Lamar services
    lamar-validate -> Runs all the Lamar container validations
               run -> Runs the configured AspNetCore application
  ---------------------------------------------------------------------------------------------
```

<[info]>
All the diagnostic commands expose an `-e` flag to control the host environment name of the running application.
<[/info]>

## Validating the Container Configuration

To validate the Lamar configuration of your system, use this command from the root of your project:

```
dotnet run -- lamar-validate
```

Running that command is going to:
1. Bootstrap your application which sets up the Lamar container
1. Checks that every service registration can find all necessary dependencies
1. If running in the default "full" mode, tries to build all known registrations one by one
1. If running in the default "full" mode, executes all the <[linkto:documentation/ioc/diagnostics/environment-tests;title=Lamar environment checks]> in your system
1. Write out the stack traces of any and all exceptions that Lamar encounters

If everything checks out, you'll get this friendly output in green:

```
Lamar registrations are all good!
```

Otherwise, you're going to get a whole lot of .Net exception strack traces with explanatory text about what registrations or environment tests failed.

The command itself will return a non zero exit code, so if `lamar-validate` is used within your automated build, it will fail your build if the validation fails **by design**.

To run a faster check of only the configuration, use the flag shown below:

```
dotnet run -- lamar-validate ConfigOnly
```

## Analyzing the Lamar Configuration

The old Lamar/StructureMap *WhatDoIHave()* diagnostic report is available from the command line in an enhanced command like this:

```
dotnet run -- lamar-services
```

This command makes heavy usage of the [Spectre.Console](https://spectresystems.github.io/spectre.console) library to format the output in a much more readable way than the old, purely textual version.

The output is somewhat ellided to eliminate some of the noise registrations added by `HostBuilder` like `IOptions<T>` or `ILogger<T>`. To see **everything**, use the verbose flag:

```
dotnet run -- lamar-services --verbose
```

<[info]>
All service registration filtering is done by the **service type** rather than the **implementation type**
<[/info]>

To filter the results to zero in on specific type registrations, you can filter by assembly:

```
dotnet run -- lamar-services --assembly YourAssemblyName
```

or by namespace (and this is inclusive):

```
dotnet run -- lamar-services --namespace NamespaceName
```

or by a specific type name:

```
dotnet run -- lamar-services --type YourTypeName
```

The filtering by type name is case insensitive, and looks for type names that contain your filter. So looking for `--type options` will find every possible registration of `IOptions<T>` for example.

To get more detailed information about exactly how Lamar is building these service registrations, use this option:

```
dotnet run -- lamar-services --build-plans
```

Lastly, you can save off the output of the `lamar-services` command to either a text file:

```
dotnet run -- lamar-services --build-plans --file services.txt
```

or keep the formatting in HTML by naming the file with either an `htm` or `html` file extension:


```
dotnet run -- lamar-services --build-plans --file services.htm
```




## Type Scanning Diagnostics

<[warning]>
The type scanning mechanics are somewhat brittle when there are dependency issues in your application, and this command can help you spot where problems may be occurring.
<[/warning]>

The <[linkto:documentation/ioc/diagnostics/type-scanning]> can be accessed at the command line with this command:

```
dotnet run -- lamar-scanning
```

## Programmatic Diagnostics

The older, programmatic usages of Lamar diagnostics are described in other sections:

<[TableOfContents]>

