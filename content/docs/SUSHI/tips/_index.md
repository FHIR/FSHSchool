---
title: "Tips and Tricks"
weight: 25
---

## Extensions for Representing Elements From Other Versions of FHIR

The FHIR specification defines behavior for a feature they refer to as [extensions for converting between versions](http://hl7.org/fhir/versions.html#extensions) (also known as "implied extensions"). This feature allows authors to represent specific elements from past and future versions of FHIR using a specific extension URL format (as described in the spec linked above). These extensions are not available in any physical package, but rather, are understood and processed conceptually.

To use this feature in SUSHI, authors must specify a dependency on a package using an id and version of the form `hl7.fhir.extensions.<extension-version>: <package-version>`, where valid extension-versions are `r2`, `r3`, `r4`, and `r5`. As an example, if an author wanted to represent the `Patient.animal.species` [element](http://hl7.org/fhir/STU3/patient-definitions.html#Patient.animal.species) as defined in STU3, the dependencies should be specified as:

```yaml
  dependencies:
    hl7.fhir.extensions.r3: 4.0.1
```

An author can then reference the extension using a URL following the format defined in the FHIR specification linked above. For example, the extension referring to the R3 `Patient.animal.species` element would be: `http://hl7.org/fhir/3.0/StructureDefinition/extension-Patient.animal.species`.

See the following documentation for additional details:
* [Dependencies](https://fshschool.org/docs/sushi/configuration/#dependencies) from the FSH School SUSHI documentation
* [Extensions for Converting Between Versions](http://hl7.org/fhir/versions.html#extensions) from the current FHIR specification

## Extension for Profiling BackboneElements

The `profile-element` extension can be used to profile a BackboneElement by pointing at another BackboneElement defined elsewhere. This is typically used to indicate that constraints on the target of a contentReference should be applied to all the references as well. For example, the following snippet indicates the all recursive references to `Questionnaire.item` (e.g., `Questionnaire.item.item`) should conform to the same constraints as the original `Questionnaire.item` in this profile:
```
Profile: MyQuestionnaire
Parent: Questionnaire
* item ^type.profile = http://example.org/StructureDefinition/MyQuestionnaire
* item ^type.profile.extension.url = http://hl7.org/fhir/StructureDefinition/elementdefinition-profile-element
* item ^type.profile.extension.valueString = "Questionnaire.item"
// ...
```

See the following documentation for additional details:
* [Extension: profile-element](https://www.hl7.org/fhir/extension-elementdefinition-profile-element.html) from the FHIR specification
* [Clarification on contentReference](https://chat.fhir.org/#narrow/stream/179252-IG-creation/topic/Clarification.20on.20contentReference/near/187852552) discussion on Zulip


## Instances of Logical Models

The IG Publisher added support for including instances of logical models as binary resources. This feature was announced and discussed in a [Logical Model Examples](https://chat.fhir.org/#narrow/stream/179252-IG-creation/topic/Logical.20Model.20Examples) thread on chat.fhir.org.  The basic steps an author needs to take in order to include logical model examples in a SUSHI project are:

1. Add the example to the `input/resources` or `input/examples` folder
    a. The file name of the example should be `Binary-{id}.json` or `Binary-{id}.xml` (substituting `{id}` for the real id)
2. Add an entry for the example in the `sushi-config.yaml` `resources` property
    a. Specify a `name`
    b. Specify `exampleCanonical` pointing to the canonical of your logical model
    c. Add an extension w/ the proper resource format (`application/fhir+json` or `application/xml`)

For example, given the following simple logical model definition in an IG w/ IG canonical root `http://example.org`:
```
Logical: MyLM
Id: MyLM
Title: "My LM"
Description: "This is mine"
* important 1..1 SU boolean "Is this resource important"
```

Create the file `input/examples/Binary-my-logical-example.json`:
```json
{
  "resourceType": "MyLM",
  "id": "my-logical-example",
  "important": true
}
```

And add the following in your `sushi-config.yaml`:
```yaml
resources:
  Binary/my-logical-example:
    extension:
      - url: http://hl7.org/fhir/StructureDefinition/implementationguide-resource-format
        valueCode: application/fhir+json
    name: Example of LM
    exampleCanonical: http://example.org/StructureDefinition/MyLM
```

This will result in your logical model example being listed and displayed as a proper example of the logical model.

{{% alert title="Note" color="success" %}}
This does not allow/support using the `Instance` keyword for creating examples of logical models. Authors must created the example as raw JSON or XML.  Support for the `Instance` keyword may come in future versions of SUSHI.
{{% /alert %}}


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