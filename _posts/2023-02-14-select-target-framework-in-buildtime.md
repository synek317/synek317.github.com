---
layout: post
title:  "Support multiple dotnet target frameworks but build using only one of them and not all"
date:   2023-02-14 12:00:00 +0100
categories: csharp
tags: [csharp]
excerpt_separator:  <!--more-->
---

Scenario: two developers working on the same project. One is using a machine with .NET 5 installed and the second one uses the latest .NET 7.

The code base is the same for both runtimes. The final application is devliered to the users as a self-contained, framework-independend exe.

The developers want to just use the `dotnet build` command and not care about each other frameworks.

How to tell dotnet to build the project using the framework that is currently installed on the machine?

The solution is both, surprisingly easy and surprisingly hard to figure it out

<!--more-->

tl;dr:

```
<PropertyGroup>
  <FrameworkDescription>$([System.Runtime.InteropServices.RuntimeInformation]::FrameworkDescription.ToLower())</FrameworkDescription>
  <TargetFramework Condition="$(FrameworkDescription.Contains('mono'))">net461</TargetFramework>
  <TargetFramework Condition="$(FrameworkDescription.Contains('net 5'))">net5</TargetFramework>
  <TargetFramework Condition="$(FrameworkDescription.Contains('net 6'))">net6</TargetFramework>
  <TargetFramework Condition="$(FrameworkDescription.Contains('net 7'))">net7</TargetFramework>
</PropertyGroup>
```

The above solution selects dynamically the target framework in the buildtime. Simple as that.

Solutions that doesn't work:
* `<TargetFrameworks>net461;net5;net6;net7</TargetFrameworks>`
It basically tells dotnet to build for _all_ frameworks and not just for _one_ of them.
* `dotnet build --framework xxx`
Surprisingly, it sitll looks for all frameworks listed in the `<TargetFrameworks>`. And it makes building project (slightly) more complex, less standard
* `<RollForward>LatestMajor</RollForward>`
While it looks like exactly what is needed - specify the lowest framework version that works for the project and then let dotnet use any newer version if available - it unfortunately doesn't work in all cases. At least it doesn't work if the `<TargetFramework>` is set to `net5` and the only installed framework on the machine is `net7`.

--

See also:
* https://learn.microsoft.com/en-us/visualstudio/msbuild/property-functions?view=vs-2022
* https://learn.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.runtimeinformation?view=net-7.0

