---
title: "Running SUSHI"
weight: 20
description: >
  Instructions for running the SUSHI tool on a FSH project
---


{{% alert title="Note" color="primary" %}}
This documentation assumes you have a SUSHI-compliant project structure and configuration as discussed in the previous sections.
{{% /alert %}}

## Running SUSHI

SUSHI is executed from the command line. The general form of the SUSHI execution command is as follows:

```shell
{{< terminal >}} sushi {options}
```

Use `sushi --version` and `sushi --help` to get basic information about the current SUSHI version.

{{% small-pageinfo color="primary" %}}
<span class="tag">SUSHI 3.0</span>For SUSHI 3.0.0 and later, the general form of the SUSHI execution command is:

```shell
$ sushi {command} {options}
```

Supported commands are `build`, `init`, `update-dependencies`, and `help` (which replaces `sushi --help`).
{{% /small-pageinfo %}}

### SUSHI Commands

SUSHI provides various commands to use with FSH projects. The following sections provide more details on each.

#### `build`

The `build` command is used to build a SUSHI project. In SUSHI 2.x, it is the default command and can be used as follows:

```shell
{{< terminal >}} sushi {fsh-project-directory} {options}
```

{{% small-pageinfo color="primary" %}}
<span class="tag">SUSHI 3.0</span>For SUSHI 3.0.0 and later, use the `build` command:

```shell
$ sushi build {fsh-project-directory} {options}
```

{{% /small-pageinfo %}}

where options include the following (in any order):

```text
-o, --out <out>          the path to the output folder
-d, --debug           output extra debugging information
-p, --preprocessed       output FSH produced by preprocessing steps
-r, --require-latest     exit with error if this is not the latest version of SUSHI (default: false)
-s, --snapshot           generate snapshot in Structure Definition output (default: false)
-h, --help               display help for command
```

Further information about each option can be found in [Build Command Option Details](#build-command-option-details).

{{% small-pageinfo color="primary" %}}
<span class="tag">SUSHI 3.0</span> SUSHI 3.0 replaces the `-d, --debug` argument with a new argument that provides more control over log output:

```
-l, --log-level <level>  specify the level of log messages (default: "info") (choices: "error", "warn", "info", "debug")
```

{{% /small-pageinfo %}}

{{% alert title="Tip" color="success" %}}
If you run SUSHI from your FSH project directory, and accept the defaults, the command can be shortened to `sushi .` or simply `sushi`.

<span class="tag">SUSHI 3.0</span> For SUSHI 3.0, you can use `sushi build .` or `sushi build`.
{{% /alert %}}

#### `init`

The `init` command is used to generate a project structure that is compatible with the FHIR IG Publisher. In SUSHI 2.x, it can be used as follows:

```shell
{{< terminal >}} sushi --init
```

{{% small-pageinfo color="primary" %}}
<span class="tag">SUSHI 3.0</span>For SUSHI 3.0.0 and later, use the `init` command:

```shell
$ sushi init
```

{{% /small-pageinfo %}}

Further details on how to use this command can be found in [Initializing a SUSHI Project](/docs/sushi/project/#initializing-a-sushi-project).

#### `update-dependencies`

{{% small-pageinfo color="primary" %}}
<span class="tag">SUSHI 3.0</span>The `update-dependencies` command is only available in SUSHI 3.0.0 and later.
{{% /small-pageinfo %}}

The `update-dependencies` command is used to update the FSH project's dependencies to the latest version. The command will check all dependencies defined in the `sushi-config.yaml` file to see if they are at the latest published version. SUSHI will output a list of all dependencies that have later versions and prompt the author whether to update. Choosing to update will directly modify the `sushi-config.yaml` file with the latest version and download the latest version of the dependency to the FHIR cache. Any dependency with a `current` or `dev` version will not be modified. This command can be used as follows:

```shell
{{< terminal >}} sushi update-dependencies {fsh-project-directory}
```

### Build Command Option Details

The following sections give further detail on using certain command line options.

### `--out`

The `--out` or `-o` flag specifies where SUSHI's output, the **fsh-generated** folder, should be written. By default, the folder will be written to the root folder, i.e., parallel to the **input** folder. When SUSHI begins, any existing content in the designated location is deleted. Within the **fsh-generated** folder, SUSHI generates file names based on the resource id (i.e., ${resourceType}-${resourceId}.json). If the id contains one or more path separators, SUSHI will sanitize file names to assure all files are written to the target directory. (_Note: the `--out` option does not refer to the output folder written by the IG Publisher_).

### `--preprocessed`

The `--preprocessed` or `-p` flag can be used to to create a folder named **_preprocessed** in SUSHI's output folder. This folder will contain representations of the input FSH after several preprocessing steps have taken place. These steps include resolution of [`Alias` values](http://build.fhir.org/ig/HL7/fhir-shorthand/branches/master/reference.html#defining-aliases), insertion of [`RuleSet` rules](http://build.fhir.org/ig/HL7/fhir-shorthand/branches/master/reference.html#defining-rule-sets), and resolution of [soft indexing](http://build.fhir.org/ig/HL7/fhir-shorthand/branches/master/reference.html#soft-indexing). This is mainly provided as a debugging tool, for the author to verify that SUSHI is preprocessing the input FSH in an expected way, and to help trace errors in the output of SUSHI back to their source. For example, if the IG Publisher reports an error on element Bundle.entry[56].resource, it may be difficult to identify the problematic entry in your FSH source if you used soft-indexing. It is much easier, however, to identify the problematic element in the preprocessed FSH that contains explicit indices.

The example below shows a FSH snippet and a preprocessed version of that snippet. In this snippet, a `Profile` is defined using a `RuleSet` and an `Alias`, and below an `Instance` is defined which uses soft indexing.

```
Alias: $CAT = http://hl7.org/fhir/ValueSet/observation-category

Profile: ObservationProfile
Parent: Observation
* insert Metadata
* category from $CAT (required)

RuleSet: Metadata
* ^version = "1.2.3"
* ^publisher = "Example publisher"

Instance: PatientInstance
InstanceOf: Patient
* name.given[+] = "John"
* name.given[+] = "Q"
```

The preprocessed version of the above FSH is shown below. The `$CAT` alias has been resolved to its full URL, the rules contained in the `RuleSet` have been inserted onto the `ObservationProfile`, and the `RuleSet` itself has been removed, and the rules on the `PatientInstance` have been resolved to fully specified paths, which do not use soft indexing.

```
Alias: $CAT = http://hl7.org/fhir/ValueSet/observation-category

// Originally defined on lines 3 - 6
Profile: ObservationProfile
Parent: Observation
Id: ObservationProfile
* ^version = "1.2.3"
* ^publisher = "Example publisher"
* category from http://hl7.org/fhir/ValueSet/observation-category (required)

// Originally defined on lines 12 - 15
Instance: PatientInstance
InstanceOf: Patient
Usage: #example
* name.given[0] = "John"
* name.given[1] = "Q"
```

{{% alert title="Note" color="primary" %}}
Once you have finished reviewing your preprocessed FSH, we recommend deleting the **_preprocessed** folder to avoid potential confusion related to multiple versions of FSH files in your project. For this same reason, we do not recommend committing your preprocessed FSH to source control.
{{% /alert %}}

### `--snapshot`

By default, SUSHI only generates the [profile differential](https://www.hl7.org/fhir/R4/profiling.html#snapshot), allowing the IG Publisher to create the [profile snapshot](https://www.hl7.org/fhir/R4/profiling.html#snapshot). This is the approach recommended by HL7 FHIR leadership. If authors prefer, the `--snapshot` (or `-s`) option can be used to cause SUSHI to generate the snapshot without having to run the IG Publisher.


## Status Messages

While SUSHI is running, it will print status messages as it processes your project files. When SUSHI has completed, you should receive a summary like the following:

```text
╔════════════════════════ SUSHI RESULTS ══════════════════════════╗
║ ╭───────────────┬──────────────┬──────────────┬───────────────╮ ║
║ │    Profiles   │  Extensions  │   Logicals   │   Resources   │ ║
║ ├───────────────┼──────────────┼──────────────┼───────────────┤ ║
║ │       1       │      1       │      1       │       1       │ ║
║ ╰───────────────┴──────────────┴──────────────┴───────────────╯ ║
║ ╭────────────────────┬───────────────────┬────────────────────╮ ║
║ │      ValueSets     │    CodeSystems    │     Instances      │ ║
║ ├────────────────────┼───────────────────┼────────────────────┤ ║
║ │         1          │         1         │         1          │ ║
║ ╰────────────────────┴───────────────────┴────────────────────╯ ║
║                                                                 ║
╠═════════════════════════════════════════════════════════════════╣
║ Fin-tastic job!                        0 Errors      0 Warnings ║
╚═════════════════════════════════════════════════════════════════╝
```

{{% alert title="Note" color="primary" %}} The following message is expected, and should be ignored:(node:46420) Warning: Accessing non-existent property 'INVALID_ALT_NUMBER' of module exports inside circular dependency (Use `node --trace-warnings ...` to show where the warning was created)
{{% /alert %}}

## Error Messages

In the process of developing your IG using FSH, you may encounter SUSHI error messages (written to the command console). Most error messages point to a specific line or lines in a `.fsh` file. If possible, SUSHI will continue, despite errors, to produce FHIR artifacts, but those artifacts may omit problematic rules. SUSHI should always exit gracefully. If SUSHI crashes, please report the issue using the [SUSHI issue tracker](https://github.com/FHIR/sushi/issues).

Here are some general tips for debugging:

* **Parsing (syntax) errors should be fixed first.** A single syntax error can balloon into many other errors, so you should always eliminate syntax errors first. Syntax error messages may include `extraneous input {x} expecting {y}`, `mismatched input {x} expecting {y}` and `no viable alternative at {x}`. These messages indicate that the line in question is not a valid FSH statement.
* **The order of keywords matters.** The declarations must start with the type of item you are creating (e.g., Profile, Instance, ValueSet).
* **The order of rules usually doesn't matter, but there are exceptions.** Slices and extensions must be created before they are constrained.
* **Rules must contain valid paths.** The `No element found at path` error means that although the overall grammar of the rule may be correct, SUSHI could not find the FHIR element you are referring to in the rule. Make sure there are no spelling errors, the element names in the path are correct, and you are using the [path grammar](https://build.fhir.org/ig/HL7/fhir-shorthand/reference.html#fsh-paths) correctly.
* **The community can help.** If you are getting an error you can't resolve, you can ask for help on the [#shorthand chat channel](https://chat.fhir.org/#narrow/stream/215610-shorthand).

### Structure Definition is Missing Snapshot Error

Some non-HL7 FHIR packages are distributed without snapshot elements in their profiles. If your IG uses one of these profiles, SUSHI will report an error like the following:

> Structure Definition http://fhir.de/StructureDefinition/observation-de-vitalsign is missing snapshot. Snapshot is required for import.

Since SUSHI does not implement its own snapshot generator, you must update the package in your FHIR cache so that its profiles include snapshot elements. Fortunately, the [Firely Terminal](https://fire.ly/products/firely-terminal/) provides a way to do this.

First, you must [install Firely Terminal](https://docs.fire.ly/projects/Firely-Terminal/InstallingFirelyTerminal.html). Then use Firely Terminal to populate the snapshot elements in the dependency package.

1. Run the command: `fhir bake --package  <packagename>`, substituting the dependency package ID for `<packagename>`.
    * E.g., `fhir bake --package de.basisprofil.r4`
2. Run SUSHI again. The error about missing snapshots should no longer be displayed.

{{% alert title="Tip" color="success" %}}
You can see a list of the available Firely Terminal versions [here](https://www.nuget.org/packages/Firely.Terminal#versions-body-tab). Version than 2.5.0-beta-7 or higher is recommended. Version 2.4.2 is not recommended because it contains a bug in the snapshot generator that adversely affects SUSHI processing.
{{% /alert %}}


## SUSHI Outputs

Based on the inputs in FSH files, **sushi-config.yaml**, and the IG project directory, SUSHI populates the **fsh-generated** directory. For example, running SUSHI on the my-project project from the [Project Structure](/docs/sushi/project/) section would add a **fsh-generated** folder as shown below:

```text
my-project
├── fsh-generated
|   └── resources
|       ├── CodeSystem-myCodeSystem.json
|       ├── Patient-myPatient-example.json
|       ├── StructureDefinition-myExtension.json
|       ├── StructureDefinition-myProfile.json
|       ├── ValueSet-myValueSet.json
|       └── ImplementationGuide-myIG.json
├── ig.ini
├── input
|   ├── fsh
│   |   └── (fsh files)
│   ├── ignoreWarnings.txt
│   ├── images
│   │   ├── myDocument.pdf
│   │   ├── myGraphic.png
│   │   └── mySpreadsheet.xlsx
│   ├── includes
│   │   └── menu.xml
│   ├── pagecontent
│   │   ├── index.md
│   │   ├── 2_mySecondPage.md
│   │   ├── 3_myThirdPage.md
│   │   └── 4_myFourthPage.md
├── package-list.json
└── sushi-config.json
```

SUSHI creates only the **fsh-generated** folder, but some of the files shown above are either processed by SUSHI to create the **ImplementationGuide.json** file, or _can_ be generated by SUSHI if the author wishes. See the breakdown of files and directories below:

* **fsh-generated\***: Generated from the definitions in the **input/fsh/\*.fsh** files.
* **ig.ini**: Specified by the author and unchanged by SUSHI.
* **input/ignoreWarnings.txt**: Specified by the author and unchanged by SUSHI.
* **input/images/\***: Specified by the author and unchanged by SUSHI.
* **input/includes/menu.xml**: Specified by the author and unchanged by SUSHI, but can alternately be specified via the `menu` property in **sushi-config.yaml**. If the `menu` configuration property is used, the output is generated to **fsh-generated/includes/menu.xml**.
* **input/pagecontent/\***: Specified by the author, numeric prefixes are used by SUSHI in generating the **ImplementationGuide-myIG.json** file.
* **package-list.json**: Specified by the author and unchanged by SUSHI.

### Downloading the IG Publisher Scripts

To run the IG Publisher, we recommend downloading the **\_updatePublisher.bat|sh** and **\_genonce.bat|sh** scripts provided by the sample-ig project. To get these scripts, [download the sample-ig project](https://github.com/FHIR/sample-ig/archive/master.zip), unzip it, and copy _all_ of the **.bat** and **.sh** files to the directory above the **fsh-generated** directory (**my-project** in the example above).

If you used `sushi --init` (or `sushi init` for SUSHI 3.0) then these scripts were already downloaded and added to your project.

### Downloading the IG Publisher

After copying these, change directories in the command prompt to the directory above the **fsh-generated** directory. At the command prompt, enter:

```shell
{{< windows >}} {{< terminal >}} _updatePublisher
```

```shell
{{< apple >}} {{< terminal >}} ./_updatePublisher.sh
```

This will download the latest version of the HL7 FHIR IG Publisher tool into the **/input-cache** directory. _This step can be skipped if you already have the latest version of the IG Publisher tool in **input-cache**._

{{% alert title="Tip" color="success" %}}
If you are blocked by a firewall, or if for any reason `_updatePublisher` fails to execute, download the current IG Publisher jar file [here](https://github.com/HL7/fhir-ig-publisher/releases/latest/download/publisher.jar). When the file has downloaded, move it into the **input-cache** directory (which you may need to create as a _sibling_ to the **input** directory).
{{% /alert %}}

### Running the IG Publisher

{{% alert title="Warning" color="warning" %}}
If you have never run the IG Publisher, you may need to install Jekyll first. See [Installing the IG Publisher](https://confluence.hl7.org/display/FHIR/IG+Publisher+Documentation) for details.
{{% /alert %}}

After the IG Publisher has been successfully downloaded, execute the following command to run it:

```shell
{{< windows >}} {{< terminal >}} _genonce
```

```shell
{{< apple >}} {{< terminal >}} ./_genonce.sh
```

This will run the HL7 IG Publisher, which may take several minutes to complete. After the publisher is finished, open the file **/output/index.html** in a browser to see the resulting IG.

{{% alert title="Tip" color="success" %}}
When running SUSHI, the IG Publisher will look for an optional **fsh.ini** control file in the root directory of the project (the same directory that contains **ig.ini**). This file should have `[FSH]` on the first line, and can include a `sushi-version` property, used to specify which version of SUSHI the IG Publisher should use, and a `timeout` property, used to set a timeout for SUSHI (in seconds). The default timeout is 60 seconds. An example **fsh.ini** file is provided below.
```text
[FSH]
sushi-version = 0.16.0
timeout = 120
```
{{% /alert %}}
