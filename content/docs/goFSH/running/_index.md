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
-o, --out <out>                   the path to the output folder
-l, --log-level <level>           specify the level of log messages: error, warn, info (default), debug
-d, --dependency <dependency...>  specify dependencies to be loaded using format dependencyId@version (FHIR R4 included by default)
-s, --style <style>               specify how the output is organized into files: file-per-definition (default), group-by-fsh-type, group-by-profile, single-file
-f, --fshing-trip                 run SUSHI on the output of GoFSH and generate a comparison of the round trip results
-i, --installed-sushi             use the locally installed version of SUSHI when generating comparisons with the "-f" option
-t, --file-type <type>            specify which file types GoFSH should accept as input: json-only (default), xml-only, json-and-xml
--indent                          output FSH with indented rules using context paths
--meta-profile <mode>             specify how meta.profile on Instances should be applied to the InstanceOf keyword: only-one (default), first, none
-v, --version                     print goFSH version
-h, --help                        display help for command
```

While GoFSH is running, it will print status messages as it processes your project files. The following sections give further detail on using certain options.

### `style`
The `style` option has four values:

* `file-per-definition`: Each standalone FSH definition is written to an individual file that is grouped in a folder according to the type of FSH definition it is. Only Aliases are combined into one `aliases.fsh` file. This is the default choice.
* `group-by-fsh-type`: Definitions are written to files based on what type of FSH definition they are (Alias, Profile, Extension, etc.).
* `group-by-profile`:  Profiles are each written to an individual file. Instances and Invariants that pertain only to a certain Profile are then included in the same file as that Profile. The remaining definitions are grouped as in the `group-by-fsh-type` option.
* `single-file`: All definitions are written to one file.

### `fshing-trip`
If this flag is added, after GoFSH runs, SUSHI will run on the output of GoFSH. The output of SUSHI will then be compared to the original input to GoFSH (FHIR is compared to FHIR), and a visualization of differences between the original input and the SUSHI output will be created in `<output-folder>/fshing-trip-comparison.html`. If the `--installed-sushi` flag is set, then this process will use whichever version of SUSHI you have globally installed. Otherwise GoFSH will use its own built-in version of SUSHI (which may not be the latest version available).

### `indent`
When the `--indent` option is specified, the output FSH will take advantage of [indented rules](http://build.fhir.org/ig/HL7/fhir-shorthand/branches/master/reference.html#indented-rules) when applicable. This will also cause `CodeSystem` definitions to utilize indentation in [hierarchical codes](http://build.fhir.org/ig/HL7/fhir-shorthand/branches/master/reference.html#defining-code-systems-with-hierarchical-codes), `Concept` designations, and `Concept` properties when applicable.

### `meta-profile`
The `--meta-profile` option can be used to control how `meta.profile` on instances should be applied to the `InstanceOf` keyword. The option has the following three values:
* `only-one` (default): If there is exactly one entry in the `meta.profile` array of a definition, this value will be used to set `InstanceOf`. If there is not exactly one entry in the `meta.profile` array, the `resourceType` of the definition will be used for `InstanceOf`, and any contents of the `meta.profile` array will be specified with `^` rules.
* `first`: If there is at least one entry in the `meta.profile` array, it will be used to set `InstanceOf`. Additional entries will be specified with `^` rules.
* `none`: The `meta.profile` array will not be used to determine `InstanceOf`.

## GoFSH Inputs

GoFSH takes FHIR StructureDefinitions and other FHIR conformance definitions (e.g., ValueSets, CodeSystems) as input. GoFSH requires that these files be JSON. Every JSON file contained in the input directory, or its subdirectories, will be processed by GoFSH into FSH.


GoFSH does not require any configuration, but if the input FHIR artifacts depend on FHIR artifacts not contained in FHIR R4, these dependencies should be specified with the `-d` flag. For example, the [mcode-cancer-patient](http://hl7.org/fhir/us/mcode/StructureDefinition-mcode-cancer-patient.html) profile in the [mCODE Implementation Guide](http://hl7.org/fhir/us/mcode/) is derived from the [us-core-patient](http://hl7.org/fhir/us/core/STU3.1/StructureDefinition-us-core-patient.html) profile in the [US Core Implementation Guide](http://hl7.org/fhir/us/core/). If you wanted to use GoFSH to convert the mcode-cancer-patient profile to FSH, you should specify US Core as a dependency:
```shell
{{< terminal >}} gofsh ./mcode-definitions -d hl7.fhir.us.core@3.1.0
```

{{% alert title="Info" color="info" %}}
GoFSH can still generate FSH when dependencies are omitted, but the resulting FSH will be incomplete.
{{% /alert %}}

## GoFSH Outputs

GoFSH populates an output directory, called **gofsh** by default. This directory will contain an **input/fsh** directory. The **input/fsh** directory will contain several **.fsh** files organized according to the `file-per-definition` style described [above](#style). Additionally, the **input/fsh** directory will contain an **index.txt** file which describes which file contains each definition. The **gofsh** directory will also contain a **sushi-config.yaml** file. If the input to GoFSH includes an ImplementationGuide resource, it is used to generate the configuration. Otherwise the configuration is generated by inferring values from the input, or by using sensible defaults if no values can be inferred. These definitions and configuration are ready to use with SUSHI, all you have to do is [run SUSHI](/docs/sushi/running).

## GoFSH Best Practices

There are a few best practices that GoFSH users should follow in order to achieve the best results.

* For best results, run GoFSH in `json-only` mode (which is the default) against FHIR definitions that are complete (i.e., have snapshots) and have been validated. This might include:
  * a package downloaded from an IG or FHIR registry (after unzipping it),
  * a package in your local FHIR cache, or
  * an IG project's **output** folder after it has been built by the IG Publisher.
* If running GoFSH against a fully built package is not possible, run GoFSH against only the **input** folder, rather than against the entire IG source folder. In this case be sure to use the proper flags depending on if your source has JSON, XML, or both.
* When possible, avoid running GoFSH on folders that contain:
  * duplicate files (i.e. definitions that are present in both **input** and **output**),
  * incomplete files (i.e. definitions without a snapshot), or
  * extra files that are not intended to be translated to FSH (e.g, **menu.xml**, files in the **input-cache** directory).
* Be aware that SUSHI may be more strict than the IG Publisher.  As a result:
  * IGs with errors in the QA report often result in FSH that does not compile cleanly,
  * sometimes issues that were not flagged by the IG Publisher may be flagged by SUSHI, and
  * occasionally technically valid constructs may result in inefficient or confusing FSH.
* Use the `--fishing-trip` option to run SUSHI on the output of GoFSH and compare the round trip results.
* Always review the results of GoFSH to ensure that they are correct and complete.
