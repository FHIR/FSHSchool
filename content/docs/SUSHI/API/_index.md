---
title: "API"
weight: 25
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
* `fhirVersion` - A string used to specify the version of FHIR to use.
* `dependencies` - An array of objects used to specify dependencies required for processing the FSH. Each object can have a `uri`, `packageId`, or `version`.
* `logLevel` - A string that specifies what level of logging to use when processing FSH. Options are `silent`, `debug`, `info`, `warn`, and `error`.


See the [configuration](/docs/sushi/configuration/#full-configuration) documentation for more information on `canonical`, `version`, `fhirVersion`, and `dependencies`. These properties correspond to the properties of the same name that are used in `sushi-config.yaml`.

## Return Value
An object with the following attributes:
* `fhir` - An array of FHIR definitions generated from the input FSH.
* `errors` - An array containing any errors detected during processing.
* `warnings` - An array containing any warnings detected during processing.

## Usage
To use `fshToFhir`, you must first install `fsh-sushi` as a dependency of your project:
```shell
{{< terminal >}} npm install fsh-sushi
```
Once `fsh-sushi` is installed as a dependency of your project, you can `import` and use this function as shown:

```javascript
import { sushiClient } from 'fsh-sushi';

sushiClient.fshToFhir('Your FSH here');
```

