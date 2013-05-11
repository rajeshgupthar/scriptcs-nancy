# ScriptCs.Nancy

A [scriptcs](https://github.com/scriptcs/scriptcs) [script pack](https://github.com/scriptcs/scriptcs/wiki/Script-Packs-master-list) for [Nancy](https://github.com/NancyFx/Nancy).

Continue your journey down the super-duper-happy-path by self-hosting Nancy with a single line of code:
```C#
Require<NancyPack>().Host();
```

Get it on [Nuget](https://nuget.org/packages/ScriptCs.Nancy/).

## Quickstart

* Create a folder, e.g. `C:\hellonancy`.
* Navigate to your folder and run `scriptcs -install ScriptCs.Nancy`.
* Create a file named `start.csx` containing the magic line of code and a sample module:

```C#
Require<NancyPack>().Host();

public class SampleModule : NancyModule
{
    public SampleModule()
    {
        Get["/"] = _ => "Hello World!";
    }
}
```

* Run `scriptcs start.csx`.

* Browse to `http://localhost:8888/`.

Congratulations! You've created your first self-hosted website using scriptcs and Nancy!

(For a slightly more advanced sample see the [sample folder](https://github.com/adamralph/scriptcs-nancy/tree/master/src/sample).)

## How it works

ScriptCs.Nancy automagically finds all modules in your script (including those loaded with [`#load`](https://github.com/scriptcs/scriptcs/wiki/Writing-a-script#loading-referenced-scripts "Loading referenced scripts")). The names of all modules found are printed in the console window (at the time of writing, scriptcs adds the prefix `"Submission#0+"` to the names of modules located in scripts).

By default, the base URL of your site is `http://localhost:8888/`. You can easily change this to another URL or even multiple URL's (see 'Advanced Usage'). All base URL's being used are printed in the console window. 

## Advanced Usage

As demonstrated above, the simplest `NancyPack` method is:
```C#
public void Host()
```

You can also use one of the richer overloads for customised behaviour:
```C#
public void Host(params Assembly[] moduleAssemblies);
public void Host(params Type[] moduleTypes);

public void Host(params string[] baseUriStrings);
public void Host(string baseUriString, IEnumerable<Assembly> moduleAssemblies);
public void Host(string baseUriString, IEnumerable<Type> moduleTypes);

public void Host(params Uri[] baseUris)
public void Host(Uri baseUri, IEnumerable<Assembly> moduleAssemblies);
public void Host(Uri baseUri, HostConfiguration configuration);
public void Host(Uri baseUri, HostConfiguration configuration, IEnumerable<Assembly> moduleAssemblies);
public void Host(Uri baseUri, HostConfiguration configuration, IEnumerable<Type> moduleTypes);
public void Host(Uri baseUri, INancyBootstrapper bootstrapper);
public void Host(Uri baseUri, INancyBootstrapper bootstrapper, HostConfiguration configuration);

public void Host(IEnumerable<Assembly> moduleAssemblies, params Uri[] baseUris);
public void Host(IEnumerable<Type> moduleTypes, params Uri[] baseUris);
public void Host(HostConfiguration configuration, params Uri[] baseUris);
public void Host(HostConfiguration configuration, IEnumerable<Assembly> moduleAssemblies, params Uri[] baseUris);
public void Host(HostConfiguration configuration, IEnumerable<Type> moduleTypes, params Uri[] baseUris);
public void Host(INancyBootstrapper bootstrapper, params Uri[] baseUris);
public void Host(INancyBootstrapper bootstrapper, HostConfiguration configuration, params Uri[] baseUris);
```

### Custom URL's

A single URL:
```C#
Require<NancyPack>().Host("http://localhost:7777/");
```

or multiple URL's:
```C#
Require<NancyPack>().Host("http://localhost:7777/", "http://localhost/hellonancy/");
```

### Hosting modules contained in an assembly

After you've installed your assembly (see the [docs](https://github.com/scriptcs/scriptcs/wiki/Writing-a-script#referencing-assemblies "scriptcs documentation")) simply call a `Host()` overload which accepts `Assembly` arguments using the name of one of your compiled modules, e.g. `MyCompiledModule`, like so:
```C#
Require<NancyPack>().Host(typeof(MyCompiledModule).Assembly);
```

**Important:** When you call a `Host()` overload which accepts `Assembly` or `Type` arguments, the automagical finding of modules in your scripts *is disabled*.

For the `Assembly` overloads, you can re-enable automagic by taking the name of one of your scripted modules, e.g. `MyScriptedModule`, and providing an extra assembly reference like so:
```C#
Require<NancyPack>().Host(typeof(MyCompiledModule).Assembly, typeof(MyScriptedModule).Assembly);
```

If you are calling one of the `Type` overloads, you can also specify the types of any scripted modules which you want to be used:
```C#
Require<NancyPack>().Host(typeof(MyCompiledModule), typeof(MyScriptedModule));
```

### Custom bootsrapper

You can use a custom bootstrapper by calling a `Host()` overload which accepts an `INancyBootstrapper`:
```C#
Require<NancyPack>().Host(new CustomBoostrapper(...));
```

The easiest way to write a custom bootstrapper is to derive from `ScriptCs.Nancy.Bootstrapper` (will soon be renamed to `DefaultNancyPackBootstrapper`). At the very least, your bootstrapper needs to provide a way to register modules since Nancy's built in auto registration does not work in the scriptcs environment. `ScriptCs.Nancy.Bootstrapper`/`DefaultNancyPackBootstrapper` provides this by taking an array of module types in the constructor. More constructor overloads will be provided in future releases.

### Managing your own host

You can also ignore the methods on `NancyPack` completely and manage your own host:
```C#
var baseUriString = "http://localhost:8888/";
using (var host = new NancyHost(myBootstrapper, new Uri(baseUriString)))
{
    host.Start();
    
    Console.WriteLine("Nancy is hosted at " + baseUriString);
    Console.WriteLine("Press any key to end");
    Console.ReadKey();
    
    host.Stop();
}
```

In this case ScriptCs is still valuable since it bootstraps your package dependencies and default namespace imports.

Have fun!

## Updates

Releases will be pushed regularly to [Nuget](https://nuget.org/packages/ScriptCs.Nancy/). For update notifications, follow [@adamralph](https://twitter.com/#!/adamralph).

To build manually, clone or fork this repository and see ['How to build'](https://github.com/adamralph/scriptcs-nancy/blob/master/how_to_build.md).

## Can I help to improve it and/or fix bugs? ##

Absolutely! Please feel free to raise issues, fork the source code, send pull requests, etc.

No pull request is too small. Even whitespace fixes are appreciated. Before you contribute anything make sure you read [CONTRIBUTING.md](https://github.com/adamralph/scriptcs-nancy/blob/master/CONTRIBUTING.md).

## What do the version numbers mean? ##

ScriptCs.Nancy uses [Semantic Versioning](http://semver.org/). The current release is 0.x which means 'initial development'. Version 1.0 will follow a stable release of scriptcs.
