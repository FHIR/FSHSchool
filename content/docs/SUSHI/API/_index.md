---
title: "API"
weight: 60
description: >
  Instructions for using SUSHI's API in a JavaScript/TypeScript project
---

SUSHI exposes a `fshToFhir` function that can be used to convert FSH strings to FHIR JSON.

## Syntax
```javascript
fshToFhir(fshString[, options])
```

## Parameters
`fshString` - A string containing FHIR Shorthand definitions.

`options` - An object which can have any combination of the following attributes:
* `canonical` - A string used to specify the canonical URL.
* `version` - A string used to specify the version.
* `fhirVersion` - A string used to specify the version of FHIR to use. Note that SUSHI only supports FHIR R4 and R5.
* `dependencies` - An array of objects used to specify dependencies required for processing the FSH. Each object should have a `packageId` and `version`, and optionally a `uri`.
* `logLevel` - A string that specifies what level of logging to use when processing FSH. Options are `silent`, `debug`, `info`, `warn`, and `error`.
* `snapshot` - (EXPERIMENTAL) A boolean that specifies whether to generate the `snapshot` data element in Structure Definition output (default: false)

{{% alert title="Warning" color="warning" %}}
The `snapshot` option, when set to true, triggers the generation of `StructureDefinition.snapshot` data elements.
Use of this option should be considered EXPERIMENTAL! The `StructureDefinition.snapshot` data elements generated
by SUSHI are likely not perfect and differ from the snapshots that the IG Publisher and/or Simplifier would create.
If you plan to publish these resources, it would be better to use one of those other tools to generate the snapshots.
{{% /alert %}}

See the [configuration](/docs/sushi/configuration/#full-configuration) documentation for more information on `canonical`, `version`, `fhirVersion`, and `dependencies`. These properties correspond to the properties of the same name that are used in `sushi-config.yaml`.

## Return Value
A `Promise` that resolves to an object with the following attributes:

* `fhir` - An array of FHIR definitions generated from the input FSH.
* `errors` - An array of objects containing any errors detected during processing. Each object has a `message` with the error message and optionally has `input` and `location` properties with additional information.
* `warnings` - An array of objects containing any warnings detected during processing. Each object has a `message` with the warning message and optionally has `input` and `location` properties with additional information.

## Usage
To use `fshToFhir`, you must first install `fsh-sushi` as a dependency of your project:
```shell
{{< terminal >}} npm install fsh-sushi
```
Once `fsh-sushi` is installed as a dependency of your project, you can `import` and use this function as shown:

```javascript
import { sushiClient } from 'fsh-sushi';

// Example basic usage
sushiClient
  .fshToFhir('Your FSH here')
  .then((results) => {
    // handle results
  })
  .catch((err) => {
    // handle thrown errors
  });

// Example usage with options
sushiClient
  .fshToFhir('Your FSH here', {
    canonical: "http://example.com",
    version: "1.2.3",
    fhirVersion: "4.0.1",
    dependencies: [{ packageId: "hl7.fhir.us.core", version: "3.1.0" }],
    logLevel: "error",
  })
  .then((results) => {
    // handle results
  })
  .catch((err) => {
    // handle thrown errors
  });
```

