# This probably isn't the repo you're looking for

As of August 2022, Plumber has been acquired by Umbraco, and has been reborn as [Umbraco.Workflow](https://github.com/umbraco/umbraco.workflow.issues).

Umbraco Workflow supports Umbraco 10+, so the only reason you'd need to raise an issue here is for Plumber on Umbraco 8, where the response will likely be to upgrade to a recent Umbraco version, upgrade to Umbraco Workflow, and see if that fixes the issue.

# Plumber - the workflow solution for Umbraco
[![Build Status](https://nathanwoulfe.visualstudio.com/Plumber%202/_apis/build/status/Plumber%208%20RTM%20CI?branchName=master)](https://nathanwoulfe.visualstudio.com/Plumber%202/_build/latest?definitionId=5&branchName=master)
[![Build Status](https://nathanwoulfe.visualstudio.com/Plumber%202/_apis/build/status/Plumber%208%20DEV%20CI?branchName=master)](https://nathanwoulfe.visualstudio.com/Plumber%202/_build/latest?definitionId=4&branchName=master)
[![NuGet release](https://img.shields.io/nuget/dt/Plumber.Workflow.svg)](https://www.nuget.org/packages/Plumber.Workflow)
[![Our Umbraco project page](https://img.shields.io/badge/our-umbraco-brightgreen.svg)](https://our.umbraco.org/projects/backoffice-extensions/plumber-workflow-for-umbraco)

Plumber adds a heap of useful bits and pieces to Umbraco, to allow multi-staged workflow approval for publish/unpublish actions. 

In the backoffice, the new Workflow section has a documentation tab with detailed explanation of features and processes, or you can [read the documentation here](DOCS.md).

## Supported Umbraco versions

Plumber supports all Umbraco versions from 8 to current (10).

For 8/9 support, install Plumber v2.

For Umbraco 10, install Plumber v10. v10 includes breaking changes from the v2 branch, which will only be an issue for implementations that extended the core product. For 'normal' installs, it's business as usual.

### Breaking changes in v10

- Async everywhere. All services and repositories are now async where possible, but function names have not changed. If you're using a Plumber service in custom code, you'll need to await the response
- Some ctors with new/reordered parameters
- Some unused constants removed
- Some unused extension methods removed
- Some extension methods replaced with Umbraco equivalents

There's a whole lot more boring stuff in the [EULA](EULA.md).

## Get started

### Install via Nuget:

```Install-Package Plumber.Workflow```

Drop a test license (an empty text file named `test.lic`) into /App_plugins/Plumber to impersonate the licensed version. The test license is restricted to sites running in debug mode, from your IDE of choice.
