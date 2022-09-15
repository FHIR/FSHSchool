---
title: "Configuration"
weight: 15
resources:
- name: minimum
  src: minimum-config.yaml
  title: Minimum Configuration Example
- name: recommended
  src: recommended-config.yaml
  title: Recommended Configuration Example
- name: exhaustive
  src: exhaustive-config.yaml
  title: Exhaustive Configuration Example
description: >
  Description of the configuration options that may be set in **sushi-config.yaml**
---

SUSHI is configured by a single **sushi-config.yaml** file. This file is written using [YAML](https://yaml.org/). Authors unfamiliar with [YAML](https://learnxinyminutes.com/docs/yaml/) should note that:
* White space (new lines and indentation) is significant
* Information is presented in `key: value` pairs
* Strings do not have to be quoted unless they contain reserved characters, such as colon (:)
* Arrays/sequences are created using `-`


## FSH Only: Minimum Configuration

If an author does not have an **ImplementationGuide** resource, and wants SUSHI to build FHIR definition files and _not_ to do any additional IG processing, the author should create a **sushi-config.yaml** with the keys `FSHOnly` (with value `true`), `canonical`, and `fhirVersion`. For example:

```yaml
FSHOnly: true
fhirVersion: 4.0.1
canonical: http://hl7.org/fhir/us/example
```

The **sushi-config.yaml** file should be located in the project's root folder.


## FSH Only: Existing ImplementationGuide Resource

If an author wants SUSHI only to build the FHIR definition files and _not_ to do any additional IG processing, AND the project contains an **ImplementationGuide** resource, then the author does not need to provide a **sushi-config.yaml** file. If there is no **sushi-config.yaml** file, SUSHI will attempt to extract the following information from the **ImplementationGuide** resource:

* `id`
* `canonical`
* `url`
* `name`
* `packageId`
* `fhirVersion`
* `version`
* `dependencies`
* `parameters`

SUSHI will then run in FSHOnly mode to produce FHIR definition files only.

To locate the **ImplementationGuide** resource, SUSHI assumes the project structure required by the [template-based IG Publisher](https://build.fhir.org/ig/FHIR/ig-guidance/), and uses the following approach:

* Look for `<root>/ig.ini`, where `<root>` is the folder containing the **input** folder. If the **ig.ini** file exists, it will have an `ig` property which gives the path to the **ImplementationGuide** resource, so SUSHI will use this path to find the resource.
* If there is no **ig.ini** in the root folder, SUSHI will search the `<root>/input` folder for an **ImplementationGuide** resource, and if exactly one resource is found, SUSHI will extract the above properties from it.

## FSH and IG Processing: Minimum Configuration

If the author wants SUSHI to do additional Implementation Guide (IG) processing, then the **sushi-config.yaml** file must provide some metadata values for the FSH project. Here is an example of a minimal configuration:

{{% show-file src="minimum" download="bottom" %}}

* For an official HL7 project, the `id` and `canonical` will typically be assigned by the [FHIR Product Director](mailto:fhir-director@hl7.org).
* `canonical` refers to the _canonical base_ of the IG, not the canonical URL of the IG resource (e.g., `http://hl7.org/fhir/us/example`, _not_ `http://hl7.org/fhir/us/example/ImplementationGuide/fhir.us.example`).
* Valid values for `status` include:
  * `draft`: The IG is still under development and is not yet considered to be ready for normal use.
  * `active`: The IG is ready for normal use.
  * `retired`: The IG has been withdrawn or superseded and should no longer be used.
  * `unknown`: It is not know which of the status values currently applies for the IG. This should be rare.
* Since SUSHI currently supports only FHIR R4 and R5, the `fhirVersion` should always be `4.0.0` or above.
* Valid values for the `releaseLabel` include:
  * `ci-build`: the continuous integration build release (not stable)
  * `draft`: draft version
  * `qa-preview`: frozen snapshot for non-ballot feedback
  * `ballot`: frozen snapshot for ballot
  * `trial-use`: official release with 'trial use' status
  * `release`: official release for use
  * `update`: official release with 'trial use' status - posted as an un-balloted STU update
  * `normative+trial-use`: official release with mixture of trial use and normative content

{{% alert title="Tip" color="success" %}}
SUSHI can generate a simple configuration file for you with the `--init` [option](/docs/sushi/project/#initializing-a-sushi-project)
{{% /alert %}}

## FSH and IG Processing: Recommended Configuration

In addition to the minimum configuration requirements shown above, most IG authors will also want to provide a `title`, `description`, `license`, `publisher`, and `dependencies`:

{{% show-file src="recommended" download="bottom" hl_lines=["4-5",7,"12-17"] %}}

{{% alert title="Note" color="primary" %}}
The `license` value should come from the [SPDX Licence Value Set](http://hl7.org/fhir/R4/valueset-spdx-license.html), although most FHIR IGs use the `CC0-1.0` (Creative Commons Zero v1.0 Universal) license.
{{% /alert %}}

### Dependencies

The `dependencies` value is a YAML object for which the keys are each dependency's package id and the values are the dependency versions. The typical format is:

  ```yaml
  dependencies:
    hl7.fhir.us.core: 3.1.0
  ```

In addition to standard version identifiers, the following two special versions are supported:

  * `dev`: indicates that the dependency should be loaded from the local FHIR cache
  * `current`: indicates that the dependency should be loaded from the last successful auto-build.

The `dependencies` property also supports an advanced syntax that allows you to directly specify the dependency id and/or URI if necessary. For example:

  ```yaml
  dependencies:
    hl7.fhir.us.core:
      id: uscore
      uri: http://hl7.org/fhir/us/core/ImplementationGuide/hl7.fhir.us.core
      version: 3.1.0
  ```

SUSHI also supports [extensions for converting between versions of FHIR](http://build.fhir.org/versions.html#extensions). To get extensions that represent elements from other versions of FHIR, a package of the form `hl7.fhir.extensions.<extension-version>:<package-version>` is used. The `<extension-version>` should be one of `r2`, `r3`, or `r4` to indicate which version of FHIR the element represented by the extension is defined in. The `<package-version>` represents which version of FHIR the extension will be used in. For an IG defined using FHIR R4, this would be `4.0.1`. As an example, if an author wanted to represent the `Patient.animal.species` [element](http://hl7.org/fhir/STU3/patient-definitions.html#Patient.animal.species) as defined in R3, the dependencies should be specified as:

```yaml
  dependencies:
    hl7.fhir.extensions.r3: 4.0.1
```

An author can then reference the extension using a URL following the format defined in the FHIR specification linked above. For example, the extension referring to the R3 `Patient.animal.species` element would be: `http://hl7.org/fhir/3.0/StructureDefinition/extension-Patient.animal.species`.

## FSH and IG Processing: Full Configuration

The table below lists all configuration properties that can be used in SUSHI's **sushi-config.yaml** file. Most SUSHI configuration properties come directly from the [Implementation Guide resource](https://www.hl7.org/fhir/R4/implementationguide.html#resource) and will be translated into the generated ImplementationGuide resource for your project. Differences between the **sushi-config.yaml** properties and ImplementationGuide properties are noted below.

| Property  | Corresponding IG element | Usage   |
| :---------------------- | :-------------------------- |:---------|
| applyExtensionMetadataToRoot | N/A | When set to true, the "short" and "definition" field on the root element of an Extension will be set to the "Title" and "Description" of that Extension. Default is true.
| canonical | N/A | The canonical base URL to be used throughout the IG |
| contact | contact | As specified in the IG resource |
| contained | contained | As specified in the IG resource |
| copyright | copyright | As specified in the IG resource |
| copyrightLabel| copyrightLabel | As specified in the IG resource - **Note:** this is an R5 IG element |
| copyrightYear or copyrightyear | N/A | Used to add a `copyrightyear` parameter to `IG.definition.parameter` |
| date | date | As specified in the IG resource |
| description | description | As specified in the IG resource |
| dependencies | dependsOn | A `key: value` pair, where key is the package id and value is the version (or `dev`/`current`). For advanced use cases, the value can be an object with keys for `id`, `uri` and `version`. For R5 IG resources, the key `reason` can also be provided. |
| experimental | experimental | As specified in the IG resource |
| extension | extension | As specified in the IG resource |
| fhirVersion | fhirVersion | As specified in the IG resource. SUSHI supports FHIR versions in the R4 or R5 sequences, as given in the [FHIR Publication History](http://hl7.org/fhir/directory.html). 5.0.0-snapshot1. Projects that wish to use a 5.0.0 pre-release can specify the version in their sushi-config.yaml file, e.g., `fhirVersion: 5.0.0-snapshot1`. |
| FSHOnly | N/A | When this flag is set to `true`, no IG-specific content will be generate. SUSHI will only convert FSH definitions to JSON files. The author at least needs to provide a `canonical` and `fhirVersion` for FSHOnly processing to succeed. When FSHOnly is false or unspecified, IG content is generated.|
| global | global | Key is the type and value is the profile |
| groups | definition.grouping | A `key: value` pair, where key is the group id and value is the description of the group. For advanced use cases, the value can be an object with keys for `name`, `description`, and `resources`. See the [Exhaustive Example](#exhaustive-example) for details. |
| id | id | As specified in the IG resource |
| implicitRules | implicitRules | As specified in the IG resource |
| instanceOptions | N/A | `key: value` pairs, where keys are `setId` and `setMetaProfile`. The `setId` value controls whether `id` is set on generated instances. Options are `always` (set `id` on all instances (the default)) or `standalone-only` (set `id` for instances where the `Usage` keyword is NOT `#inline`). The `setMetaProfile` value controls whether `meta.profile` is set on generated instances. It can have the following values: `always` (set meta.profile for all instances (the default)), `never` (do not set meta.profile on any instances), `inline-only` (set `meta.profile` only for instances of profiles with `Usage` keyword set to `#inline`), or `standalone-only` (set `meta.profile` for instances where the `Usage` keyword is NOT `#inline`). |
| jurisdiction | jurisdiction | As specified in the IG resource |
| language | language | As specified in the IG resource |
| license | license | As specified in the IG resource |
| menu | N/A | Used to generate the `fsh-generated/includes/menu.xml` file. The key is the menu item name and the value is the URL. Menus can contain sub-menus, but the IG Publisher currently only supports sub-menus one level deep. See the [Exhaustive Example](#exhaustive-example) for details. Authors can provide their own `menu.xml` by removing this property and placing a `menu.xml` file in `/input/includes`. |
| meta | meta | As specified in the IG resource |
| modifierExtension | modifierExtension | As specified in the IG resource |
| name | name | As specified in the IG resource |
| packageId | packageId | As specified in the IG resource. If unspecified, defaults to `id`. |
| pages | definition.page | SUSHI can auto-generate pages, but authors can manage pages through this property. If this property is used, SUSHI will not generate any page entries. The YAML key is the file name containing the page. The title key-value pair provides the title for the page. If a title is not provided, then the title will be generated from the file name. If a generation value (corresponding to definition.page.generation) is not provided, it will be inferred from the file name extension. In the IG resource, pages can contain sub-pages; so in the config file, any sub-properties that are valid filenames with supported extensions (e.g., .md/.xml) will be treated as sub-pages. See the [Exhaustive Example](#exhaustive-example) for details. |
| parameters | definition.parameter | Consists of key-value pairs where the keys are values of `definition.parameter.code`. For R5 IG resources, the keys can be a FSH code that specifies the `code` and `system` values of `definition.parameter.code`, which is a Coding. See the [Exhaustive Example](#exhaustive-example) for details. If a parameter allows repeating values, the value in the YAML may be a sequence/array. For example, the `path-resource` parameter specifies relative paths to additional folders that contain predefined resources (see [Specifying Additional Resource Paths](/docs/sushi/tips/#specifying-additional-resource-paths)). |
| publisher | publisher, with cardinality changed to 0..* | Publisher can be a single item or a list, each with a name and optional url and/or email. The first publisher's name will be used as IG.publisher.  The contact details and/or additional publishers will be translated into IG.contact values |
| releaseLabel or releaselabel | N/A | Used to add a `releaseLabel` parameter to `IG.definition.parameter` |
| resources | definition.resource | SUSHI can auto-generate a list of resources based on FSH definitions and provided JSON or XML resources, but this property can be used to add additional entries or override generated entries. SUSHI uses the {resource type}/{resource name} format as the YAML key (corresponding to IG.definition.resource.reference). Authors can specify the value "omit" to omit a FSH-generated resource from the resource list. `groupingId` can be used, but top-level groups syntax may be a better option. |
| status | status | As specified in the IG resource |
| templates | definition.template | As specified in the IG resource |
| text | text | As specified in the IG resource |
| title | title | As specified in the IG resource |
| url | url | As specified in the IG resource. If unspecified, defaults to `{canonical}/ImplementationGuide/{id}`. |
| useContext | useContext | As specified in the IG resource |
| version | version | As specified in the IG resource |
| versionAlgorithmString | versionAlgorithm[x] |  As specified in the IG resource - **Note:** this is an R5 IG element |
| versionAlgorithmCoding | versionAlgorithm[x] |  As specified in the IG resource - **Note:** this is an R5 IG element |

## Exhaustive Example

The following provides an exhaustive example **sushi-config.yaml** covering many of the properties discussed above.

{{% show-file src="exhaustive" download="bottom" %}}
