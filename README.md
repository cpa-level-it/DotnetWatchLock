# DotnetWatchLock

## The problem
This repository illustrate a problem using `dotnet watch` in collaboration of `net8`, `Visual Studio 2022` and `iterative source generators`. When rebuilding the application from the `dotnet watch` console, it seems that it tries to copy the generator dll which is locked by `Visual Studio 2022` which makes the build fails.

This seems to only happens when
* using `net8`.
* using `Iterative Source Generators` that are directly referenced in the solution (not a nuget package).
* having the solution loaded in a `Visual Studio 2022` instance.

This workflow was working correctly using `net7`.

## How to reproduce
* First build at least one time the solution, then close `Visual Studio 2022`.
* Re-open the solution in `Visual Studio 2022`, you can check that the source generator is working by navigating to the `Analyser` node of the `DotnetWatchLock` project.
* Launch the `DotnetWatchLock` project in the `Watch` configuration, you can use `CTRL+F5` shortcut to do this.
* Once the `dotnet watch` console is open, wait for the project to build and be launched.
* Rebuilt the solution using `CTRL+R` in the `dotnet watch` console.
* The console should then output the following error : 
```
C:\Program Files\dotnet\sdk\8.0.100-rc.1.23455.8\Microsoft.Common.CurrentVersion.targets(4702,5): warning MSB3026: Coul
d not copy "obj\Debug\netstandard2.0\DotnetWatchSourceGenerator.dll" to "bin\Debug\netstandard2.0\DotnetWatchSourceGene
rator.dll". Beginning retry 2 in 1000ms. The process cannot access the file 'D:\projects\git\DotnetWatchLock\DotnetWatc
hSourceGenerator\bin\Debug\netstandard2.0\DotnetWatchSourceGenerator.dll' because it is being used by another process.
The file is locked by: ".NET Host (28964)" [D:\projects\git\DotnetWatchLock\DotnetWatchSourceGenerator\DotnetWatchSourc
eGenerator.csproj]
```
* Closing the console and launching `dotnet watch` again allows to build the project.

You can test that it was working correctly with `net7` by renaming `global.json_net7` to `global.json` in the `DotnetWatchLock` project.
