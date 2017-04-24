# MSBuild Configuration Defaults [![Build status](https://ci.appveyor.com/api/projects/status/b04pij25k0dbfll9?svg=true)](https://ci.appveyor.com/project/PedroLamas/msbuildconfigurationdefaults "Build Status") [![Latest stable version](https://img.shields.io/nuget/v/MSBuildConfigurationDefaults.svg?style=flat)](https://www.nuget.org/packages/MSBuildConfigurationDefaults/ "Latest stable version")

MSBuild based projects have two default build configurations: Debug and Release.

While these two configurations are enough for most projects, some might actually require custom build configurations that will support different environments, alternative build targets, etc..

Until now we could use Visual Studio Configuration Manager to easily create a copy an existing configuration setup, and then change small bits to match our specifications.

But now there's a [new csproj format for .NET Core](https://docs.microsoft.com/en-us/dotnet/articles/core/tools/csproj), and while it includes the expected Debug and Release build configurations, the "copy configuration" process doesn't work anymore!

The problem is that the new project format is based in quite a few implicit defaults, so Visual Studio Configuration Manager can't actually create a copy of the existing build configurations with all the properties set.

Ideally, one would like to "inherit" from the base Debug and Release configurations, and then just override specific properties.

This is where the `MSBuildConfigurationDefaults` NuGet package comes in!

## Installation

Install the NuGet package by running the following command:

```
Install-Package MSBuildConfigurationDefaults
```

After this step, closing and re-opening the solution is recommended to ensure the build scripts changes are correctly loaded.

## Usage

Once installed, any build configuration with name starting or ending on "Debug" will have the following build properties set by default:

```xml
<DefineConstants>$(DefineConstants);DEBUG;TRACE</DefineConstants>
<DebugSymbols>true</DebugSymbols>
<DebugType>full</DebugType>
<Optimize>false</Optimize>
```

Similarly, any build configuration with name starting or ending on "Release" will have the following build properties set by default:

```xml
<DefineConstants>$(DefineConstants);RELEASE;TRACE</DefineConstants>
<DebugSymbols>false</DebugSymbols>
<DebugType>portable</DebugType>
<Optimize>true</Optimize>
```

If any of these properties are set on the project, those values will have override the defaults above.

If you don't want to name your custom build definition according to the rules above, just add a `ConfigurationGroup` property and set the value to `Debug` or `Release` to ensure those build definitions get the appropriate default properties set.

## Examples

The following is an example of a custom build configuration called "Production", that has the default values set as for the "Release" build configuration, but also overrides the `DebugSymbols` property default value:

```xml
<PropertyGroup Condition="'$(Configuration)' == 'Prodution'">
  <ConfigurationGroup>Release</ConfigurationGroup>
  <DebugSymbols>true</DebugSymbols>
</PropertyGroup>
```