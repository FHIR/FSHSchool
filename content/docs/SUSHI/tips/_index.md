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
>     uri: http://my-fhir-ig.org/ImplementationGuide/123
>     version: 1.1.0
> ```

As the error suggests, you need to update your `sushi-config.yaml` file so the dependency declaration includes both the `uri` and `version`. The `uri` is intended to point to an ImplementationGuide resource, but in this case there is no ImplementationGuide resource to point to.

We recommend using the root canonical for the dependency package as the `uri` value. If you don't know the root canonical, you can sometimes guess it by reviewing several of the package's resources to see if there is a common base URL. For example, after reviewing the files in the [de.basisprofil.r4 package](https://simplifier.net/packages/de.basisprofil.r4/1.1.0/~files), you might choose `http://fhir.de` as its `uri`.

{{% alert title="Note" color="primary" %}}
When you use the root canonical (or another URI) as the dependency URI, the IG Publisher may report the following error in the QA: _The canonical URL for an Implementation Guide must point directly to the implementation guide resource, not to the Implementation Guide as a whole_. Since the dependency does not have an ImplementationGuide resource, you can safely ignore and/or suppress the warning.
{{% /alert %}}

