---
title: "Running GoFSH"
weight: 10
---

## Running GoFSH

GoFSH is executed from the command line. The general form of the GoFSH execution command is as follows:

```shell
{{< terminal >}} gofsh {input-directory} {options}
```

where options include the following (in any order):

```text
-o, --out <out>                   the path to the output directory (default: ./fsh)
-l, --log-level <level>           specify the level of log messages: error, warn, info (default), debug
-d, --dependency <dependency...>  specify dependencies to be loaded using format dependencyId@version (FHIR R4 included by default)
-v, --version                     print goFSH version
-h, --help                        output usage information
```

While GoFSH is running, it will print status messages as it processes your project files.

## GoFSH Inputs

As input, GoFSH takes FHIR StructureDefinitions. GoFSH requires that these files be JSON. Every JSON file contained in the input directory, or its subdirectories, will be processed by GoFSH into FSH.

GoFSH does not require any configuration, but if the input FHIR artifacts depend on FHIR artifacts not contained in FHIR R4, these dependencies should be specified with the `-d` flag. For example, the [mCODE Implementation Guide](http://hl7.org/fhir/us/mcode/) contains definitions which are derived from the [US Core Implementation Guide](http://hl7.org/fhir/us/core/), see [mcode-cancer-patient](http://hl7.org/fhir/us/mcode/StructureDefinition-mcode-cancer-patient.html), which is derived from [us-core-patient](http://hl7.org/fhir/us/core/STU3.1/StructureDefinition-us-core-patient.html). If you wanted to use GoFSH to convert the mcode-cancer-patient profile to FSH, you should specify US Core as a dependency:
```shell
{{< terminal >}} gofsh ./mcode-definitions -d hl7.fhir.us.core@3.1.0
```

{{% alert title="Info" color="info" %}}
GoFSH can still generate FSH when dependencies are omitted, but the resulting FSH will be incomplete.
{{% /alert %}}

## GoFSH Outputs

GoFSH populates its output directory with a single file, called **resources.fsh**, which contains FSH definitions for all of the input FHIR StructureDefinitions. These definitions are ready to use with SUSHI, all you have to do is add a [configuration file](/docs/sushi/configuration).