---
title: "Introduction"
weight: 5
type: legacy
---
<style>
body {background-color: #E2F0D9;}
</style>

{{% alert title="Warning" color="warning" %}}
This documentation is for Legacy versions of SUSHI (pre-1.0.0). Head to the [Documentation](/docs) tab for documentation on the current version of SUSHI.
{{% /alert %}}

[FHIR Shorthand](https://build.fhir.org/ig/HL7/fhir-shorthand/reference.html) ("FSH" or "Shorthand") is a specially-designed language for defining the content of Health Level Seven (HL7®) [Fast Healthcare Interoperability Resources (FHIR®)](https://www.hl7.org/fhir/R4/overview.html) Implementation Guides (IGs). It is simple and compact, with tools to produce Fast Healthcare Interoperability Resources (FHIR) profiles, extensions and IGs.

[SUSHI](/docs/docs-legacy/sushi/) ("SUSHI Unshortens ShortHand Inputs") is a reference implementation of an interpreter/compiler for the FSH language. SUSHI produces FHIR profiles, extensions, and other artifacts needed to create FHIR Implementation Guides (IG).

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

## Understanding FHIR Shorthand

Some sections of FSH School assume a basic understanding of FHIR Shorthand. You can access the FHIR Shorthand documentation at any time by clicking the "FHIR Shorthand" link in the left-hand column.

We recommend new FSH authors start with the [overview](https://build.fhir.org/ig/HL7/fhir-shorthand/index.html) and review the [language reference](https://build.fhir.org/ig/HL7/fhir-shorthand/reference.html) when additional details are needed.

## Understanding FHIR Profiling

Most FSH School resources assume a basic understanding of FHIR and FHIR profiling. You can access the FHIR documentation at any time by clicking the "FHIR R4" link in the left-hand column. You may find the following more specific links helpful as well:

* [Profiling](http://hl7.org/fhir/R4/profiling.html): Provides information and guidance on profiling topics such as constraining and slicing.
* [Extensibility](http://hl7.org/fhir/R4/extensibility.html): Provides information about using and defining extensions.
* [Resources](http://hl7.org/fhir/R4/resourcelist.html): Lists all FHIR resources. Useful for referencing available elements as you write profiles.
* [DataTypes](http://hl7.org/fhir/R4/datatypes.html): Lists all FHIR datatypes. Useful for understanding types declared in resource elements.
* [StructureDefinition](http://hl7.org/fhir/R4/structuredefinition.html): Documents StructureDefinition (basis for resources, profiles, and extensions). Useful for authoring top-level caret rules.
* [ElementDefinition](http://hl7.org/fhir/R4/elementdefinition.html#ElementDefinition): Documents ElementDefinition (basis for elements in resources, profile, and extensions). Useful for authoring path-specific caret rules.
* [Guidance for FHIR IG Creation](https://build.fhir.org/ig/FHIR/ig-guidance/): (Work in Progress) Documents the new template-based publishing framework.
* [IG Publisher](https://confluence.hl7.org/display/FHIR/IG+Publisher+Documentation): Documents how to use and configure the HL7 IG Publisher.

</body>