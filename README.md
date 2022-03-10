# Plumber - the workflow solution for Umbraco 8+
[![Build Status](https://nathanwoulfe.visualstudio.com/Plumber%202/_apis/build/status/Plumber%208%20RTM%20CI?branchName=master)](https://nathanwoulfe.visualstudio.com/Plumber%202/_build/latest?definitionId=5&branchName=master)
[![Build Status](https://nathanwoulfe.visualstudio.com/Plumber%202/_apis/build/status/Plumber%208%20DEV%20CI?branchName=master)](https://nathanwoulfe.visualstudio.com/Plumber%202/_build/latest?definitionId=4&branchName=master)
[![NuGet release](https://img.shields.io/nuget/dt/Plumber.Workflow.svg)](https://www.nuget.org/packages/Plumber.Workflow)
[![Our Umbraco project page](https://img.shields.io/badge/our-umbraco-brightgreen.svg)](https://our.umbraco.org/projects/backoffice-extensions/plumber-workflow-for-umbraco)

Plumber adds a heap of useful bits and pieces to Umbraco, to allow multi-staged workflow approval for publish/unpublish actions. 

In the backoffice, the new Workflow section has a documentation tab with detailed explanation of features and processes, or you can [read the documentation here](DOCS.md).

## Where's the code?
Noticed this repo doesn't have any code? That's because Plumber is a licensed product, and the code is currently private. That may change in the future, but for the immediate future at least, it is how it is.

Plumber will always have a free version. It still requires a valid license, but comes at no cost. The free version does have some restrictions on features and functionality (5 group maximum, some advanced features disabled), but those aside, is still a full-featured solution and will be perfect for smaller, less complex implementations.

The paid license is a one-off, &euro;900 one-time payment. All features, no restrictions, no ongoing cost.

There's a whole lot more boring stuff in the [EULA](EULA.md).

## Get started

### Install via Nuget:

```Install-Package Plumber.Workflow```

Drop a test license (an empty text file named `test.lic`) into /App_plugins/Plumber to impersonate the licensed version. The test license is restricted to sites running in debug mode, from your IDE of choice.
