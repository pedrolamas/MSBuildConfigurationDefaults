# MSBuild Configuration Defaults [![Build status](https://ci.appveyor.com/api/projects/status/b04pij25k0dbfll9?svg=true)](https://ci.appveyor.com/project/PedroLamas/msbuildconfigurationdefaults "Build Status") [![Latest stable version](https://img.shields.io/nuget/v/MSBuildConfigurationDefaults.svg?style=flat)](https://www.nuget.org/packages/MSBuildConfigurationDefaults/ "Latest stable version")

MSBuild based projects have two default build configurations: Debug and Release.

While these two configurations are enough for the majority of projects, some might require developers to create custom build configurations (to support different environments, alternative build targets, ...)!

Visual Studio Configuration Manager makes it easy to create a new build configuration by copying an existing build configuration setup, and then making whatever changes are required.

But now there's a [new csproj format for .NET Core](https://docs.microsoft.com/en-us/dotnet/articles/core/tools/csproj), which includes the expected Debug and Release build configurations.

Problem is that the new format is based in quite a few implicit defaults, so Visual Studio Configuration Manager can't actually create a proper copy of the existing build configurations.

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