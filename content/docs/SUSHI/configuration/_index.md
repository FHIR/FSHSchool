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
---

The HL7 FHIR IG Publisher relies on several configuration files, including **ig.ini**, **package-list.json**, **menu.xml**, and an instance of the ImplementationGuide resource. Splitting information among multiple files and managing different formats makes IG configuration difficult to manage.

SUSHI offers the same functionality in a single **sushi-config.yaml** file, allowing all configuration to be in a consistent format with no duplication of information. This file is written using [YAML](https://yaml.org/). Authors unfamiliar with [YAML](https://learnxinyminutes.com/docs/yaml/) should note that:
* White space (new lines and indentation) is significant
* Information is presented in `key: value` pairs
* Strings do not have to be quoted unless they contain reserved characters, such as colon (:)
* Arrays/sequences are created using `-`

## Minimum Configuration

At a minimum, the **sushi-config.yaml** file must provide a few high-level metadata values for the FSH project, if the author wishes for SUSHI to do additional Implementation Guide (IG) processing:

{{% show-file src="minimum" download="bottom" %}}

* For an official HL7 project, the `id` and `canonical` will typically be assigned by the [FHIR Product Director](mailto:fhir-director@hl7.org).
* Valid values for `status` include:
  * `draft`: The IG is still under development and is not yet considered to be ready for normal use.
  * `active`: The IG is ready for normal use.
  * `retired`: The IG has been withdrawn or superseded and should no longer be used.
  * `unknown`: It is not know which of the status values currently applies for the IG. This should be rare.
* Since SUSHI currently supports only FHIR R4, the `fhirVersion` should always be `4.0.1`.
* Valid values for the `releaseLabel` include:
  * `ci-build`: the continuous integration build release (not stable)
  * `draft`: draft version
  * `qa-preview`: frozen snapshot for non-ballot feedback
  * `ballot`: frozen snapshot for ballot
  * `trial-use`: official release with 'trial use' status
  * `release`: official release for use
  * `update`: official release with 'trial use' status - posted as an un-balloted STU update
  * `normative+trial-use`: official release with mixture of trial use and normative content
* The `template` value consists of a template id and version separated by `#`. This value will be reflected in the generated **ig.ini** file for your project. For the most up-to-date list of templates, see the [Guidance for FHIR IG Creation](https://build.fhir.org/ig/FHIR/ig-guidance/#technical-details).

{{% alert title="Tip" color="success" %}}
SUSHI can generate a simple configuration file for you with the `--init` [option](/docs/sushi/project/#initializing-a-sushi-project)
{{% /alert %}}

#### FSH-Only

If an author wants SUSHI only to build the FHIR definition files, and _not_ to do any additional IG processing, and if the project contains an **ImplementationGuide** resource, then the author does not need to provide a **config.yaml** at all. If there is no **config.yaml**, SUSHI will automatically attempt to extract the following information from an **ImplementationGuide** resource:
* `canonical`
* `fhirVersion`
* `version`
* `dependencies`

SUSHI will then run in FSH-Only mode to produce FHIR definition files only.

When attempting to extract information from an **ImplementationGuide** resource, SUSHI assumes the project structure required by the [template-based IG Publisher](https://build.fhir.org/ig/FHIR/ig-guidance/). The following approach is used to find the **ImplementationGuide** resource:
* Look for `<root>/ig.ini`, where `<root>` is the folder containing the **fsh** subdirectory. If the **ig.ini** file exists, it will have an `ig` property which gives the path to the **ImplementationGuide** resource, so SUSHI will use this path to find the resource.
* If there is no **ig.ini** in the root folder, SUSHI will search the `<root>/input` folder for an **ImplementationGuide** resource, and if exactly one resource is found, SUSHI will extract the above properties from it.

If an author does not have an **ImplementationGuide** resource, but still wants SUSHI to build FHIR definition files only, the author should add a `FSHOnly` flag to the **config.yaml** and set its value to `true`:

```yaml
FSHOnly: true
```
However, the author will also at least need to provide a `canonical` and `fhirVersion` in the configuration for FSH-Only processing to succeed.

## Recommended Configuration

In addition to the minimum configuration requirements shown above, most IG authors will also want to provide a `title`, `description`, `license`, `publisher`, and `dependencies`:

{{% show-file src="recommended" download="bottom" hl_lines=["4-5",7,"12-17"] %}}

* The `license` value should come from the [SPDX Licence Value Set](http://hl7.org/fhir/R4/valueset-spdx-license.html), although most FHIR IGs use the `CC0-1.0` (Creative Commons Zero v1.0 Universal) license.
* The `dependencies` value is a YAML object for which the keys are each dependency's package id and the values are the dependency versions. In addition to standard version identifiers, the following two special versions are supported:
  * `dev`: indicates that the dependency should be loaded from the local FHIR cache
  * `current`: indicates that the dependency should be loaded from the last successful auto-build.
* The `dependencies` property also supports an advanced syntax that allows you to directly specify the dependency URI if necessary. For example:
  ```yaml
  dependencies:
    hl7.fhir.us.mcode:
      uri: http://hl7.org/fhir/us/mcode
      version: 1.0.0
  ```

## Full Configuration

The table below lists all configuration properties that can be used in SUSHI's **sushi-config.yaml** file. Most SUSHI configuration properties come directly from the [Implementation Guide resource](https://www.hl7.org/fhir/R4/implementationguide.html#resource) and will be translated into the generated ImplementationGuide resource for your project. Differences between the **sushi-config.yaml** properties and ImplementationGuide properties are noted below.

| Property  | Corresponding IG element | Usage   |
| :---------------------- | :-------------------------- |:---------|
| id | id | As specified in the IG resource |
| meta | meta | As specified in the IG resource |
| implicitRules | implicitRules | As specified in the IG resource |
| language | language | As specified in the IG resource |
| text | text | As specified in the IG resource |
| contained | contained | As specified in the IG resource |
| extension | extension | As specified in the IG resource |
| modifierExtension | modifierExtension | As specified in the IG resource |
| url | url | As specified in the IG resource. If not specified, defaults to `{canonical}/ImplementationGuide/{id}`. |
| version | version | As specified in the IG resource |
| name | name | As specified in the IG resource |
| title | title | As specified in the IG resource |
| status | status | As specified in the IG resource |
| experimental | experimental | As specified in the IG resource |
| date | date | As specified in the IG resource |
| publisher | publisher, with cardinality changed to 0..* | Publisher can be a single item or a list, each with a name and optional url and/or email. The first publisher's name will be used as IG.publisher.  The contact details and/or additional publishers will be translated into IG.contact values |
| contact | contact | As specified in the IG resource |
| description | description | As specified in the IG resource |
| useContext | useContext | As specified in the IG resource |
| jurisdiction | jurisdiction | As specified in the IG resource |
| copyright | copyright | As specified in the IG resource |
| packageId | packageI | As specified in the IG resource. If not specified, defaults to `id`. |
| license | license | As specified in the IG resource |
| fhirVersion | fhirVersion | As specified in the IG resource |
| dependencies | dependsOn | A `key: value` pair, where key is the package id and value is the version (or `dev`/`current`). For advanced use cases, the value can be an object with keys for `uri` and `version`.|
| global | global | Key is the type and value is the profile |
| groups | definition.grouping | A `key: value` pair, where key is the name of the group and value is the description of the group |
| resources | definition.resource | SUSHI can auto-generate a list of resources based on FSH definitions and provided JSON or XML resources, but this property can be used to add additional entries or override generated entries. SUSHI uses the {resource type}/{resource name} format as the YAML key (corresponding to IG.definition.resource.reference). Authors can specify the value "omit" to omit a FSH-generated resource from the resource list. `groupingId` can be used, but top-level groups syntax may be a better option. |
| pages | definition.page | SUSHI can auto-generate pages, but authors can manage pages through this property. If this property is used, SUSHI will not generate any page entries. The YAML key is the file name containing the page. The title key-value pair provides the title for the page. If a title is not provided, then the title will be generated from the file name. If a generation value (corresponding to definition.page.generation) is not provided, it will be inferred from the file name extension. In the IG resource, pages can contain sub-pages; so in the config file, any sub-properties that are valid filenames with supported extensions (e.g., .md/.xml) will be treated as sub-pages. |
| parameters | definition.parameter | The key is the code. If a parameter allows repeating values, the value in the YAML may be a sequence/array. |
| templates | definition.template | As specified in the IG resource |
| copyrightYear or copyrightyear | N/A | Used to add a `copyrightyear` parameter to `IG.definition.parameter` |
| releaseLabel or releaselabel | N/A | Used to add a `releaseLabel` parameter to `IG.definition.parameter` |
| canonical | N/A | The canonical URL to be used throughout the IG |
| template | N/A | Template used in `ig.ini` file. <br><br> Authors can provide their own `ig.ini` file by removing this property and placing an `ig.ini` file in the root of their project directory. |
| menu | N/A | Used to generate the input/index.md file. The key is the menu item name and the value is the URL. Menus can contain sub-menus, but the IG Publisher currently only supports sub-menus one level deep. <br><br> Authors can provide their own `menu.xml` by removing this property and placing a `menu.xml` file in `/input/includes` |
| history | N/A | Used to create a `package-list.json`. SUSHI will use the existing top-level properties in its config to populate the top-level package-list.json properties: package-id, canonical, title, and introduction. Authors who wish to provide different values can supply them as properties under history. All other properties under history are assumed to be versions. <br><br> Additionally, the current version is special. If the author provides only a single string value, it is assumed to be the URL path to the current build. The following default values will then be used: `desc: Continuous Integration Build` (latest in version control), `status: ci-build`, and `current: true`. <br><br> Authors can provide their own `package-list.json` by removing this property and placing a `package-list.json` file in the root of their project. |
| indexPageContent | N/A | This property is provided for backwards compatibility reasons, and its use is discouraged. It was used to specify the content of `index.md`, however, authors should provide their own index file by not using this property and placing an `index.md` or `index.html` file in `input/pages` or `input/pagecontent`.  |
| FSHOnly | N/A | When this flag is set to `true`, no IG specific content will be generated, SUSHI will only convert FSH definitions to JSON files. When false or unset, IG content is generated.

## Exhaustive Example

The following provides an exhaustive example **sushi-config.yaml** covering many of the properties discussed above.

{{% show-file src="exhaustive" download="bottom" %}}
