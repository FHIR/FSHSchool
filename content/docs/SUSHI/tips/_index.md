---
title: "Tips and Tricks"
weight: 25
---

## Resolving Issues with Dependency IGs

Sometimes authors need to take additional steps when depending on non-HL7 package dependencies (e.g., packages from Simplifier). The following sections describe common problems and their solutions.

### SUSHI could not find the IG URL in the dependency IG

SUSHI requires that dependency packages have an associated IG URL. This is because SUSHI needs it to properly populate the [dependsOn](http://build.fhir.org/implementationguide-definitions.html#ImplementationGuide.dependsOn) property when generating your IG's ImplementationGuide resource.

When SUSHI loads a dependency that does not have an ImplementationGuide resource in it and/or does not declare a canonical in its `package.json` file, SUSHI reports an error like the following:

> Failed to add de.basisprofil.r4:1.1.0 to ImplementationGuide instance because SUSHI could not find the IG URL in the dependency IG. To specify the IG URL in your sushi-config.yaml, use the dependency details format:
>
> ```yaml
> dependencies:
>   de.basisprofil.r4:
>     uri: http://fhir.org/packages/de.basisprofil.r4/ImplementationGuide/de.basisprofil.r4
>     version: 1.1.0
> ```

As the error suggests, you need to update your `sushi-config.yaml` file so the dependency declaration includes both the `uri` and `version`. The `uri` is intended to point to an ImplementationGuide resource, but in this case there is no ImplementationGuide resource to point to.

The FHIR core team has recently looked into this and now recommends using a `uri` with the following format when a package has no IG resource: `http://fhir.org/packages/${packageId}/ImplementationGuide/${packageId}`. In the example above, authors should provide the `uri` value: `http://fhir.org/packages/de.basisprofil.r4/ImplementationGuide/de.basisprofil.r4`.

In the near future, SUSHI will be updated to automatically apply this guidance so that authors will no longer receive this error and will not need to take any action.

### Structure Definition is missing snapshot

Some non-HL7 FHIR packages are distributed without snapshot elements in their profiles. If your IG uses one of these profiles, SUSHI will report an error like the following:

> Structure Definition http://fhir.de/StructureDefinition/observation-de-vitalsign is missing snapshot. Snapshot is required for import.

Since SUSHI does not implement its own snapshot generator, you must update the package in your FHIR cache so that its profiles include snapshot elements. Luckily, the [Firely Terminal](https://fire.ly/products/firely-terminal/) provides a way to do this.

First, you must install Firely Terminal:
1. Install the [.NET Core 3.1](https://dotnet.microsoft.com/en-us/download/dotnet/3.1) SDK for your operating system.
2. Confirm that the .NET Core 3.1 SDK is properly installed by running the command: `dotnet --version`.
    * If the command fails, exit your terminal and start a new terminal session. Then try step 2 again.
    * If `dotnet --version` reports a different version than the 3.1 SDK, you will need to follow the instructions to [select the .NET version to use](https://docs.microsoft.com/en-us/dotnet/core/versions/selection#the-sdk-uses-the-latest-installed-version). When creating the _global.json_ file, be sure to specify the full SDK version number (e.g., `3.1.416`).
3. Run the command: `dotnet tool install --global firely.terminal --version 2.5.0-beta-7`.
4. Confirm that Firely Terminal is properly installed by running the command: `fhir --version`.
    * If the command fails, you may need to add the .NET tools folder to your path. On Mac, run the following command: `export PATH=$PATH:~/.dotnet/tools`. Then try step 4 again.

Then use Firely Terminal to populate the snapshot elements in the dependency package.
1. Run the command: `fhir bake --package  <packagename>`, substituting the dependency package ID for `<packagename>`.
    * E.g., `fhir bake --package de.basisprofil.r4`
2. Run SUSHI again. The error about missing snapshots should no longer be displayed.

{{% alert title="Tip" color="success" %}}
You can see a list of the available Firely Terminal versions [here](https://www.nuget.org/packages/Firely.Terminal). If there is a later version than 2.5.0-beta-7, we recommend that. We do not recommend the 2.4.2 version because it contains a bug in the snapshot generator that adversely affects SUSHI processing.
{{% /alert %}}