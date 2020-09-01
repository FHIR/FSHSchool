---
title: "Project Structure"
weight: 10
---

## Simple FSH Projects

The simplest FSH project (sometimes referred to as a "FSH tank") contains only a configuration file and FSH files containing FHIR Shorthand definitions. For example, a simple FSH tank might look like this:

```text
simple-project
├── sushi-config.yaml
├── file1.fsh
├── file2.fsh
└── file3.fsh
```

The **sushi-config.yaml** file provides project configuration data to SUSHI. It is described further in the [Configuration](/docs/sushi/configuration/) documentation.

Each FSH file can contain multiple FSH definitions of varying types. FSH file names are not significant, but must end with the **.fsh** extension. In addition, FSH files can be organized into subdirectories. This provides authors the flexibility to organize their FSH definitions in whatever way makes sense to then.

{{% alert title="Tip" color="success" %}}
A simple FSH project like the one shown above can be used with a minimal **sushi-config.yaml** file to create a bare-bones IG . It can also be used to generate only the FHIR resources by specifying the `FSHOnly` flag in **sushi-config.yaml**.
{{% /alert %}}

### Using a fsh Subdirectory with the HL7 IG Publisher and Auto-Builder

If a project is intended to be built by the HL7 IG Publisher [Auto-Builder](https://github.com/FHIR/auto-ig-builder/blob/master/README.md), the FSH tank should be located in an **input/fsh** subdirectory:

```text
simple-ig
└── input
    └── fsh
        ├── config.yaml
        ├── file1.fsh
        ├── file2.fsh
        └── file3.fsh
```

When the IG Publisher detects an **input/fsh** subdirectory, it will automatically run SUSHI on that directory and output the SUSHI results to an **input/generated** subdirectory (e.g., **simple-ig/input/generated** in the example above). It will then continue with the normal IG Publisher process.

This approach allows a GitHub repository to be configured such that whenever changes to FSH files are pushed to GitHub, the [Auto-Builder](https://github.com/FHIR/auto-ig-builder/blob/master/README.md) will pick them up, run the SUSHI/IG Publisher process, and publish the resulting IG to [http://build.fhir.org](http://build.fhir.org).

{{% alert title="Note" color="primary" %}}
Using an **input/fsh** subdirectory is not required, but since many authors prefer this option, the remaining documentation assumes an **input/fsh** subdirectory in its file structures.
{{% /alert %}}

## IG Projects

SUSHI provides support for many of the files and directories required by the [template-based IG Publisher](https://build.fhir.org/ig/FHIR/ig-guidance/) for building Implementation Guides. Many IG customizations can be configured using additional properties in the **sushi-config.yaml** file.

Alternatively, authors can use their own custom content for **package-list.json**, **ig.ini**, **menu.xml**, **index.md** (or **index.xml**), pages, images, and other IG Publisher inputs.  For example, a FSH project for a customized IG might look like this:

```text
customized-ig
├── ig.ini
├── input
|   ├── fsh
|   |   ├── sushi-config.yaml
|   |   ├── file1.fsh
|   |   ├── file2.fsh
|   |   └── file3.fsh
│   ├── ignoreWarnings.txt
│   ├── images
│   │   ├── myDocument.pdf
│   │   ├── myGraphic.png
│   │   └── mySpreadsheet.xlsx
│   ├── includes
│   │   └── menu.xml
│   └── pagecontent
│       ├── 1_mySecondPage.md
│       ├── 2_myThirdPage.md
│       ├── 3_myFourthPage.md
│       └── index.md
└── package-list.json
```

You can populate your project (under **input/fsh** above) as follows:

* **sushi-config.yaml**: This required file provides project configuration data to SUSHI. It is described further in the [Configuration](/docs/sushi/configuration/) documentation.
* **\*.fsh**: FSH files contain the FHIR Shorthand definitions for all the resources and examples in your IG.
* **ig.ini**: If present and no `template` property is specified in **sushi-config.yaml**, the user-provided file will be used instead of a generated one.
* **input/ignoreWarnings.txt**: If present, this file can be used to suppress specific QA warnings and information messages during the FHIR IG publication process.
* **input/images/\***: Put anything that is not a page in the IG, such as images, spreadsheets or zip files, in the **input/images** subdirectory. These files can be referenced by user-provided pages or menus.
* **input/includes/menu.xml**: If present and no `menu` property is specified in **sushi-config.yaml**, this file will be used for the IG's main menu layout.
* **input/pagecontent/\***: Put either markup (.xml) or markdown (.md) files with the narrative content of your IG in the **input/pagecontent/** subdirectory. These files are the sources for the html pages that accompany the automatically-generated pages of your IG. The header and footer of these pages are automatically generated, so your content should not include these elements. Any number of pages can be added. In addition to stand-alone pages, you can provide additional text for generated artifact pages. The naming of these files is significant:
  * **index.xml\|md**: This file provides the content for the IG's main page, unless the `indexPageContent` property is specified in **sushi-config.yaml**, in which case this file should not exist. Providing an index file is strongly recommended over inlining the content in **sushi-config.yaml**.
  * **N\_pagename.xml\|md**: If present, these files will be generated as individual pages in the IG. The leading integer (N) determines the order of the pages in the table of contents.
  * **{artifact-file-name}-intro.xml\|md**: If present, the contents of the file will be placed on the relevant page _before_ the artifact's definition.
  * **{artifact-file-name}-notes.xml\|md**: If present, the contents of the file will be placed on the relevant page _after_ the artifact's definition.
* **input/{supported-resource-input-directory}/\*** (not shown above): JSON or XML files in [supported resource directories](https://build.fhir.org/ig/FHIR/ig-guidance/using-templates.html#root.input) (e.g., **profiles**, **extensions**, **examples**, etc.) can be referenced by FHIR artifacts defined in FSH.
* **package-list.json**: This optional file, described [here](https://confluence.hl7.org/display/FHIR/FHIR+IG+PackageList+doco), should contain the version history of your IG. If present and no `history` property is specified in **sushi-config.yaml**, it will be used instead of a generated **package-list.json**.

{{% alert title="Tip" color="success" %}}
Examples of **package.json**, **ig.ini**, **package-list.json**, **ignoreWarnings.txt** and **menu.xml** files can be found in the [sample IG project](https://github.com/FHIR/sample-ig) provided for this purpose. In addition, more general guidance can be found in [Guidance for HL7 IG Creation](https://build.fhir.org/ig/FHIR/ig-guidance/).
{{% /alert %}}

### Initializing a SUSHI Project
Setting up this project structure manually can be complex, so to simplify that process, SUSHI provides an `--init` option. Running `sushi --init` will cause SUSHI to create a new SUSHI project with a default configuration and project structure. This provides a simple way to get started with FHIR Shorthand and SUSHI.

When `sushi --init` is run, SUSHI will request high-level project information from the user:
```text
Name (Default: ExampleIG): NewIG
Id (Default: fhir.example): my.id
Canonical (Default: http://example.org): http://myid.org
Status (Default: draft): active
Version (Default: 0.1.0): 2.0.0
Initialize SUSHI project in C:\Users\shorty\dev\NewIG? [y/n]: y
```
These values are used to generate a simple **sushi-config.yaml** file and a corresponding IG-Publisher-compatible project structure:
```text
NewIG
├── .gitignore
├── _genonce.bat     
├── _genonce.sh           
├── _updatePublisher.bat  
├── _updatePublisher.sh 
├── _gencontinuous.bat 
├── _gencontinuous.sh
└── input
    ├── fsh
    |   ├── sushi-config.yaml
    |   └── patient.fsh
    └── pagecontent
        └── index.md    
```
In addition to the contents of the `input/fsh` folder, `--init` adds several `.bat` and `.sh` scripts which allow you to [run the IG Publisher](/docs/sushi/running/#downloading-the-ig-publisher), and a default `.gitignore` file for a FSH project. From this point on, the author can modify the configuration and definitions as necessary.
