---
title: "Documentation"
linkTitle: "Documentation"
menu:
  main:
    weight: 20
aliases:
  - /docs/introduction/index.html
  - /docs/tutorials/index.html
---

This is the documentation for **SUSHI** and **GoFSH**. (This is *not* the [FSH documentation](https://hl7.org/fhir/uv/shorthand/reference.html); see the following section for more details.)

### What is FSH?

[FSH (**F**HIR **Sh**orthand)](https://hl7.org/fhir/uv/shorthand/) is a specially-designed language for defining the content of [HL7](https://hl7.org/) [FHIR](https://www.hl7.org/fhir/R4/overview.html) [Implementation Guides](https://www.hl7.org/fhir/implementationguide.html) (IGs). It is designed to be simple and compact, and along with SUSHI can be used to produce FHIR profiles, extensions, and IGs.

If you are new to FHIR and FSH, or want more information, [please see below](#getting-started-with-fhir-and-fsh).

### What is SUSHI?

SUSHI (**S**USHI **U**nshortens **Sh**ortHand **I**nputs) is a FSH compiler. SUSHI converts FSH language to [FHIR artifacts](https://www.hl7.org/fhir/R4/overview.html). SUSHI can run in stand-alone mode or as part of the [HL7 IG Publisher](https://confluence.hl7.org/display/FHIR/IG+Publisher+Documentation).

**→ [SUSHI documentation](/docs/sushi/)**

### What is GoFSH?

GoFSH is a converter that takes FHIR artifacts (e.g., profiles, extensions, value sets, instances) and produces equivalent FSH. GoFSH is essentially the opposite of SUSHI. GoFSH helps you transition to FSH if you have an existing Implementation Guide produced by other methods.

**→ [GoFSH documentation](/docs/gofsh/)**

<br>

----

## FSH School Conventions

The following style conventions are used throughout the FSH School content:

| Style | Explanation | Example |
|:------------|:------|:---------|
| `Code` | Code fragments, such as commands, FSH statements, and syntax expressions  | `* status = #open` |
| `{curly braces}` | An item to be substituted in a syntax expression | `{display string}` |
| `<datatype>` | An element or path to an element with the given data type, to be substituted in the syntax expression | `<CodeableConcept>`
| _italics_ | An optional item in a syntax expression | <code><i>"{string}"</i></code> |
| ellipsis (...) | Indicates a pattern that can be repeated | <code>{flag1} {flag2} {flag3}&nbsp;...</code>
| **bold** | A directory path or file name | **example-1.fsh** |

In addition, the following symbols are used in documented commands:

| Symbol | Explanation |
|:----------:|:------|
| {{< apple >}} | Indicates information or command specific to OS X. Commands can be run within the Terminal application. |
| {{< windows >}} | Indicates information or command specific to Windows. A command window can be launched by typing `cmd` at the _Search Windows_ tool. |
| {{< terminal >}} | Represents command prompt (may vary depending on platform) |

<br>

----


## Getting started with FHIR and FSH

### FHIR and FHIR Profiling

Most FSH School resources assume a basic understanding of FHIR and FHIR profiling. A good way to get started is to go through our [**self-service FSH Seminar course**: Getting Started with New Tools to Create Swimmingly Slick Implementation Guides](/courses/). This covers how to read FHIR IGs and how to use FSH for [FHIR Profiling](https://hl7.org/fhir/profiling.html).

Readers who are new to FHIR should also view the [FHIR Specification](https://hl7.org/fhir/) (also linked in the right-hand sidebar). You may find the following more specific links helpful as well:

* [Profiling](http://hl7.org/fhir/profiling.html): Provides information and guidance on profiling topics such as constraining and slicing.
* [Extensibility](http://hl7.org/fhir/extensibility.html): Provides information about using and defining extensions.
* [Resources](http://hl7.org/fhir/resourcelist.html): Lists all FHIR resources. Useful for referencing available elements as you write profiles.
* [DataTypes](http://hl7.org/fhir/datatypes.html): Lists all FHIR datatypes. Useful for understanding types declared in resource elements.
* [StructureDefinition](http://hl7.org/fhir/structuredefinition.html): Documents StructureDefinition (basis for resources, profiles, and extensions). Useful for authoring top-level caret rules.
* [ElementDefinition](http://hl7.org/fhir/elementdefinition.html#ElementDefinition): Documents ElementDefinition (basis for elements in resources, profile, and extensions). Useful for authoring path-specific caret rules.

Additionally, these other resources may be of interest:

* [Guidance for FHIR IG Creation](https://build.fhir.org/ig/FHIR/ig-guidance/): (Work in Progress) Documents the new template-based publishing framework.
* [IG Publisher](https://confluence.hl7.org/display/FHIR/IG+Publisher+Documentation): Documents how to use and configure the HL7 IG Publisher.

### FSH

We recommend new FSH authors start with the [overview](https://hl7.org/fhir/uv/shorthand/) and review the [language reference](https://hl7.org/fhir/uv/shorthand/reference.html) that are part of official FSH Specification when additional details are needed.

Additionally, *all* FSH authors are strongly recommended to go on the [Deep Dive with FSH](https://fshschool.org/courses/fsh-seminar/04-deep-dive-with-fsh.html) (part of our [self-service FSH Seminar course](/courses/)).