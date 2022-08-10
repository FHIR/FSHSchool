---
title: "API"
weight: 25
description: >
  Instructions for using GoFSH's API in a JavaScript/TypeScript project
---

GoFSH exposes a `fhirToFsh` function that can be used to convert FHIR to FSH.

## Syntax
```javascript
fhirToFsh(fhir[, options])
```

## Parameters
`fhir` - An array of FHIR resources, represented either as strings or JSON.

`options` - An object which can have any combindation of the following attributes:
* `dependencies` - An array of strings used to specify dependencies required for processing the FHIR. Dependencies should use the format `<packageId>@<version>` (example: `hl7.fhir.us.core@3.0.1`).
* `logLevel` - A string that specifies what level of logging to use when processing FHIR. Options are `silent`, `debug`, `info`, `warn`, and `error`.
* `style` - A string representing how the returned output is styled. The options are:
  * `string` - The generated FSH will be returned in one single string. This is the default.
  * `map` - The generated FSH will be returned as an object. The attributes on the object are:
    * `aliases` - A string containing all `Alias` definitions.
    * `profiles` - A `Map` containing all `Profile` definitions as values.
    * `extensions` - A `Map` containing all `Extension` definitions as values.
    * `codeSystems` - A `Map` containing all `CodeSystem` definitions as values.
    * `valueSets` - A `Map` containing all `ValueSet` definitions as values.
    * `instances` - A `Map` containing all `Instance` definitions as values.
    * `invariants` - A `Map` containing all `Invariant` definitions as values.
    * `mappings` - A `Map` containing all `Mapping` definitions as values.

    For each `Map`, the keys are the name of the FSH definition. For example, if the definition was:
    ```
    Profile: MyPatient
    Parent: Patient
    ```
    The key would be `MyPatient`.

## Return Value
A `Promise` that resolves to an object with the following attributes:
* `fsh` - The generated FSH, styled according to the `style` parameter.
* `configuration` - An object representing the `sushi-config.yaml` file that would be generated if GoFSH was running in a command line interface.
* `errors` - An array of strings containing any errors detected during processing.
* `warnings` - An array of strings containing any warnings detected during processing.

## Usage
To use `fhirToFsh`, you must first install `gofsh` as a dependency of your project:
```shell
{{< terminal >}} npm install gofsh
```
Once `gofsh` is installed as a dependency of your project, you can `import` and use this function as shown:

```javascript
import { gofshClient } from 'gofsh';

// Example basic usage
gofshClient
  .fhirToFsh(['{ Your FHIR here }'])
  .then((results) => {
    // handle results
  })
  .catch((err) => {
    // handle thrown errors
  });

// Example usage with options
gofshClient
  .fhirToFsh(['{ Your FHIR here }'], {
    dependencies: ["hl7.fhir.us.mcode@1.0.0"],
    style: "map",
    logLevel: "silent",
  })
  .then((results) => {
    // handle results
  })
  .catch((err) => {
    // handle thrown errors
  });
```

