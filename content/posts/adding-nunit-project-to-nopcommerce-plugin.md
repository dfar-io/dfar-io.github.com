---
title: "Adding Nunit Project to nopCommerce Plugin"
date: 2021-01-12T11:59:19-05:00
draft: false
---

I've been working on integrating SonarClodu into the nopCommerce Plugin
development workflow, and came across a procedure that helps with building
out a plugin with the ability to easily integrate it into a fresh copy of
nopCommerce.

As an example, you can see it in the [FreshAddress Integration Plugin source code](https://github.com/dfar-io/freshaddress-integration-for-nopcommerce).

## Plugin Folder Structure

````
.github/workflows
Source
    Plugin Project
Tests
    Test project
.gitignore
LICENSE
README.md
sonar-helper.sh
````

A few things to take apart here:
* `sonar-helper.sh` is a helper script that adds a flag to all other nopCommerce
projects to prevent them from being included in Sonar report.

## Creating the Test Project

When creating the Test project, I've set it up to mimic the nopCommerce tech stack _(you can change the naming based on ypur plugin)_:

````
dotnet new nunit -n Nop.Plugin.Misc.FreshAddressIntegration.Tests -o Tests

dotnet add package coverlet.msbuild
dotnet add package FluentAssertions
dotnet add package Moq

dotnet add reference ../Source/Nop.Plugin.Misc.FreshAddress.csproj
````

Then when running tests with coverage, you can run with the following:
````
dotnet test Plugins/Tests/Nop.Plugin.Misc.FreshAddress.Tests.csproj /p:CollectCoverage=true /p:CoverletOutputFormat=opencover
````

This will generate the report that gets imported into Sonar and provides reporting.