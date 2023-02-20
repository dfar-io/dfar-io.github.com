---
title: "Fixing MSBuild TARGETPATH Issue"
date: 2023-02-20T14:05:10Z
draft: false
categories:
    - Technology
---

Ran into an issue that was causing the following error on GitLab when trying
to build a package:

```
/usr/share/dotnet/sdk/7.0.103/Sdks/NuGet.Build.Tasks.Pack/build/NuGet.Build.Tasks.Pack.targets(221,5): error NU5026: The file '${TARGETPATH}' to be packed was not found on disk. [/hugo servbuilds/organization/My.Project/My.Project/My.Project.csproj]
```

The solution is to define TargetPath explicitly in all `.csproj` files by adding a PropertyGroup section like so:

```
<PropertyGroup>
    <TargetPath>$(ProjectDir)$(OutputPath)$(ProjectFileName)</TargetPath>
</PropertyGroup>
```