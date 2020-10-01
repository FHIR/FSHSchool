---
title: "Running SUSHI"
weight: 20
---


{{% alert title="Note" color="primary" %}}
This documentation assumes you have a SUSHI-compliant project structure and configuration as discussed in the previous sections.
{{% /alert %}}

## Running SUSHI

SUSHI is executed from the command line. The general form of the SUSHI execution command is as follows:

```shell
{{< terminal >}} sushi {specification-directory} {options}
```

where options include the following (in any order):

```text
-o, --out <out>  the path to the output folder
-d, --debug      output extra debugging information
-s, --snapshot   generate snapshot in Structure Definition output (default: false)
-i, --init       initialize a SUSHI project
-v, --version    print SUSHI version
-h, --help       output usage information
```

{{% alert title="Tip" color="success" %}}
If you run SUSHI from your FSH project directory, and accept the defaults, the command can be shortened to `sushi .`
{{% /alert %}}


{{% alert title="Note" color="primary" %}}
By default, SUSHI only generates the [profile differential](https://www.hl7.org/fhir/R4/profiling.html#snapshot), allowing the IG Publisher to create the [profile snapshot](https://www.hl7.org/fhir/R4/profiling.html#snapshot). This is the approach recommended by HL7 FHIR leadership. If authors prefer, the `-s` option can be used to cause SUSHI to generate the snapshot without having to run the IG Publisher.
{{% /alert %}}

While SUSHI is running, it will print status messages as it processes your project files. When SUSHI has completed, you should receive a summary like the following:

```text
╔════════════════════════ SUSHI RESULTS ══════════════════════════╗
║ ╭──────────┬────────────┬───────────┬─────────────┬───────────╮ ║
║ │ Profiles │ Extensions │ ValueSets │ CodeSystems │ Instances │ ║
║ ├──────────┼────────────┼───────────┼─────────────┼───────────┤ ║
║ │    1     │     1      │     1     │      1      │     1     │ ║
║ ╰──────────┴────────────┴───────────┴─────────────┴───────────╯ ║
║                                                                 ║
╠═════════════════════════════════════════════════════════════════╣
║ O-fish-ally error free!                0 Errors      0 Warnings ║
╚═════════════════════════════════════════════════════════════════╝
```

### Error Messages

In the process of developing your IG using FSH, you may encounter SUSHI error messages (written to the command console). Most error messages point to a specific line or lines in a `.fsh` file. If possible, SUSHI will continue, despite errors, to produce FHIR artifacts, but those artifacts may omit problematic rules. SUSHI should always exit gracefully. If SUSHI crashes, please report the issue using the [SUSHI issue tracker](https://github.com/FHIR/sushi/issues).

Here are some general tips for debugging:

* **Parsing (syntax) errors should be fixed first.** A single syntax error can ballooon into many other errors, so you should always eliminate syntax errors first. Syntax error messages may include `extraneous input {x} expecting {y}`, `mismatched input {x} expecting {y}` and `no viable alternative at {x}`. These messages indicate that the line in question is not a valid FSH statement.
* **The order of keywords matters.** The declarations must start with the type of item you are creating (e.g., Profile, Instance, ValueSet).
* **The order of rules usually doesn't matter, but there are exceptions.** Slices and extensions must be created before they are constrained.
* **Rules must contain valid paths.** The `No element found at path` error means that although the overall grammar of the rule may be correct, SUSHI could not find the FHIR element you are referring to in the rule. Make sure there are no spelling errors, the element names in the path are correct, and you are using the [path grammar](https://build.fhir.org/ig/HL7/fhir-shorthand/reference.html#fsh-paths) correctly.
* **The community can help.** If you are getting an error you can't resolve, you can ask for help on the [#shorthand chat channel](https://chat.fhir.org/#narrow/stream/215610-shorthand).


## SUSHI Outputs

Based on the inputs in FSH files, **sushi-config.yaml**, and the IG project directory, SUSHI populates the output directory. For example, the customized-ig project from the [Project Structure](/docs-beta/sushi/project/) section would result in output like the following:

```text
customized-ig
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

Note the following files and directories from the output:

* **fsh-generated\***: Generated from the definitions in the **input/fsh/\*.fsh** files.
* **ig.ini**: Specified by the author and unchanged by SUSHI.
* **input/ignoreWarnings.txt**: Specified by the author and unchanged by SUSHI.
* **input/images/\***: Specified by the author and unchanged by SUSHI.
* **input/includes/menu.xml**: Specified by the author and unchanged by SUSHI, but can alternately be specified via the `menu` property in **sushi-config.yaml**. If the `menu` configuration property is used, the output is generated to **fsh-generated/includes/menu.xml**.
* **input/pagecontent/\***: Specified by the author, numeric prefixes are used by SUSHI in generating the **ImplementationGuide-myIG.json** file.
* **package-list.json**: Specified by the author and unchanged by SUSHI.

### Downloading the IG Publisher

{{% alert title="Note" color="primary" %}}
The following steps require the **\_updatePublisher.bat|sh** and **\_genonce.bat|sh** scripts in the output directory that SUSHI writes to. If these scripts are not present there, you will need to download them from the sample-ig project. The easiest way to do this is to [download the sample-ig project](https://github.com/FHIR/sample-ig/archive/master.zip), unzip it, and copy _all_ of the **.bat** and **.sh** files to the directory that SUSHI writes to.
{{% /alert %}}

After running SUSHI, change directories to the output directory (if necessary). At the command prompt, enter:

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
