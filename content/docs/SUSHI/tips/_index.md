---
title: "Tips and Tricks"
weight: 25
description: >
  Helpful tips and tricks that may be useful when running SUSHI on more complex FSH projects
---

## Specifying Additional Resource Paths

Sometimes authors may wish to put predefined resources in folders other than the normally supported `input` sub-folders. To support this, SUSHI now recognizes the [ImplementationGuide parameter](http://build.fhir.org/codesystem-guide-parameter-code.html) `path-resource`. Authors can include this parameter in `sushi-config.yaml` to specify relative paths to additional folders that contain predefined resources. For example, the following can now be used in `sushi-config.yaml` to include resources from the sub-folder `predefined-resources`:

  ```yaml
  parameters:
    path-resource:
      - input/predefined-resources
  ```

Note that the value of `path-resource` is an array (or sequence) formatted in the standard YAML style.

For SUSHI users who manage their own ImplementationGuide resource (i.e. _FSHOnly_ IGs), SUSHI will use the parameter from the ImplementationGuide resource:

  ```json
  "parameter": [
    {
      "code": "path-resource",
      "value": "input/predefined-resource"
    }
  ]
  ```

## Support for contentReference Elements

Content references allow ElementDefinitions to refer to be defined in terms of other elements. A typical application of a content reference is to prevent infinite regress in elements such as `Questionnaire.item.item`, whose the definition refers back to `Questionnaire.item` via a content reference.

Content references are supported in `AddElementRules` by way of the `contentReference` keyword. This feature applies to Logical Models and custom Resources. To define an element of type `contentReference`, include `contentReference <url-to-referenced-element>` in the `AddElementRule` where the `type` would typically go. For example, the following `AddElementRule` will create an element `section.section` that is a contentReference to the element found at `http://example.org/StructureDefinition/Report#Report.section`.

  ```
  * section.section 0..1 contentReference http://example.org/StructureDefinition/Report#Report.section "A sub-section"
  ```

## Inferred Choice Path

When assigning values to choice elements (e.g., value[x]) on an Instance, type-specific elements (e.g., valueBoolean) should always be used in assignment rules. However, if the choice element has been constrained to a single type, SUSHI will infer the correct type-specific element.  

For example, SUSHI will infer the last line is to be interpreted as `* valueBoolean = true` as the `Instance` is exported. Authors are encouraged to not depend on this behavior, and use type-specific elements in instances.

  ```
  Profile: BooleanObservation
  Parent: Observation
  * value[x] only boolean

  Instance: MyObservation
  InstanceOf: BooleanObservation
  * status = #final
  * code = http://example#somecode
  * value[x] = true
  ```

## Referencing the Canonical URL of the ImplementationGuide

There may be cases where an author needs to refer to the canonical URL of the ImplementationGuide resource they are creating. SUSHI now supports using the `Canonical` keyword with the IG's `id`, `packageId`, or `name` that is specified in `sushi-config.yaml`. For example, when creating a `CapabilityStatement`, the following Assignment Rule can be added:

```
* implementationGuide = Canonical(my-package-id)
```

## Extensions for Representing Elements From Other Versions of FHIR

The FHIR specification defines behavior for a feature they refer to as [extensions for converting between versions](http://hl7.org/fhir/versions.html#extensions) (also known as "implied extensions"). This feature allows authors to represent specific elements from past and future versions of FHIR using a specific extension URL format (as described in the spec linked above). These extensions are not available in any physical package, but rather, are understood and processed conceptually.

To use this feature in SUSHI, authors must specify a dependency on a package using an id and version of the form `hl7.fhir.extensions.<extension-version>: <package-version>`, where valid extension-versions are `r2`, `r3`, `r4`, and `r5`. As an example, if an author wanted to represent the `Patient.animal.species` [element](http://hl7.org/fhir/STU3/patient-definitions.html#Patient.animal.species) as defined in STU3, the dependencies should be specified as:

```yaml
  dependencies:
    hl7.fhir.extensions.r3: 4.0.1
```

An author can then reference the extension using a URL following the format defined in the FHIR specification linked above. For example, the extension referring to the R3 `Patient.animal.species` element would be: `http://hl7.org/fhir/3.0/StructureDefinition/extension-Patient.animal.species`.

See the following documentation for additional details:
* [Dependencies](https://fshschool.org/docs/sushi/configuration/#dependencies) from the FSH School SUSHI documentation
* [Extensions for Converting Between Versions](http://hl7.org/fhir/versions.html#extensions) from the current FHIR specification

## Extension for Profiling BackboneElements

The `profile-element` extension can be used to profile a BackboneElement by pointing at another BackboneElement defined elsewhere. This is typically used to indicate that constraints on the target of a contentReference should be applied to all the references as well. For example, the following snippet indicates that all recursive references to `Questionnaire.item` (e.g., `Questionnaire.item.item`) should conform to the same constraints as the original `Questionnaire.item` in this profile:

```
Profile: MyQuestionnaire
Parent: Questionnaire
* item ^type.profile = "http://example.org/StructureDefinition/MyQuestionnaire"
* item ^type.profile.extension.url = "http://hl7.org/fhir/StructureDefinition/elementdefinition-profile-element"
* item ^type.profile.extension.valueString = "Questionnaire.item"
// ...
```

See the following documentation for additional details:
* [Extension: profile-element](https://www.hl7.org/fhir/extension-elementdefinition-profile-element.html) from the FHIR specification
* [Clarification on contentReference](https://chat.fhir.org/#narrow/stream/179252-IG-creation/topic/Clarification.20on.20contentReference/near/187852552) discussion on Zulip

## Instances of Logical Models

The IG Publisher supports including instances of logical models as binary resources. This feature was announced and discussed in a [Logical Model Examples](https://chat.fhir.org/#narrow/stream/179252-IG-creation/topic/Logical.20Model.20Examples/near/251192344) thread on chat.fhir.org.

Starting with SUSHI 3.0.0, authors can use `Instance:` to create instances of logical models and instances of logical model profiles in the same way as all other FSH instances. These instances are exported using standard JSON serialization and automatically receive `id` and `meta.profile` values when the logical model and SUSHI configuration support those elements. These instances have filenames starting with `Binary-` and will be auto-encoded as part of the publishing process.

Alternatively, authors can provide their own instances of logical models without defining them in FSH. The basic steps an author needs to take in order to manually include logical model examples in a SUSHI project are:

1. Add the example to the `input/resources` or `input/examples` folder
    - The file name of the example should be `Binary-{id}.json` or `Binary-{id}.xml` (substituting `{id}` for the real id)
2. Add an entry for the example in the `sushi-config.yaml` `resources` property
    - Specify a `name`
    - Specify `exampleCanonical` pointing to the canonical of your logical model
    - Add an extension w/ the proper resource format (`application/fhir+json` or `application/xml`)

For example, given the following simple logical model definition in an IG w/ IG canonical root `http://example.org`:

```
Logical: MyLM
Id: MyLM
Title: "My LM"
Description: "This is mine"
* important 1..1 SU boolean "Is this resource important"
```

Create the file `input/examples/Binary-my-logical-example.json`:

```json
{
  "resourceType": "MyLM",
  "id": "my-logical-example",
  "important": true
}
```

And add the following in your `sushi-config.yaml`:

```yaml
resources:
  Binary/my-logical-example:
    extension:
      - url: http://hl7.org/fhir/StructureDefinition/implementationguide-resource-format
        valueCode: application/fhir+json
    name: Example of LM
    exampleCanonical: http://example.org/StructureDefinition/MyLM
```

Both approaches will result in your logical model example being listed and displayed as a proper example of the logical model.

## Manual Slice Ordering

{{% small-pageinfo color="primary" %}}
<span class="tag">SUSHI 3.0</span>The manual slice ordering option is only available in SUSHI 3.0.0 and later.
{{% /small-pageinfo %}}

Starting in SUSHI `v3.0.0`, authors can exercise full manual control over the ordering of slice elements within Instances. Previous versions of SUSHI allowed for partial control of slice element ordering, but some ordering was determined by SUSHI's implementation and could not be affected by an author. In the current version of SUSHI (`v3.0.0` or later), authors can configure their FSH projects to manually control slice ordering. When using manual slice ordering, authors should use soft indexing and avoid using hard numeric indices.

Manual slice ordering follows the following rules:

* slices appear on an Instance in the order of FSH rules
* any required slices (`1..*`) that are not referenced in a FSH rule on the Instance appear after all referenced slices in the order in which they are defined on the Instance's StructureDefinition (the instance's `InstanceOf`)
* a rule that references a sliced element should reference it using the slice name
* to reference multiple items in a slice, use hard or soft indices after the slice name (e.g., `component[myslice][0]`)
* when rule paths use only a soft index (instead of a slice name), the resolved numeric index will take into account named slices that have already been referenced by previous rule paths. This should only be used to add items that do not belong to a defined slice.
  * Note: when manual slice ordering is enabled, it is not possible to refer to an element with a slice name by soft numeric index only. If hard numeric indices are used (not recommended), they may still directly access previously referenced named slices. This may lead to undesired output.


To use this ordering, add the following to `sushi-config.yaml`:

```yaml
instanceOptions:
  manualSliceOrdering: true
```

### Examples

```
Profile: ExampleBPObservation
Parent: Observation
// slicing rules for component omitted for brevity
* component contains systolicBP 1..1 MS and diastolicBP 1..1 MS
```

When using this profile without manual slice ordering, the `systolicBP` slice will always be the first entry in the `component` element, and the `diastolicBP` slice will always be the second entry in the `component` element. So, the following two instances would have the same `component` elements:
```
Instance: ExampleByName
InstanceOf: ExampleBPObservation
// some required elements omitted for brevity
* component[systolicBP].valueQuantity = 108 'mm[Hg]'
* component[diastolicBP].valueQuantity = 45 'mm[Hg]'

Instance: ExampleByNumber
InstanceOf: ExampleBPObservation
// some required elements omitted for brevity
* component[0].valueQuantity = 108 'mm[Hg]'
* component[1].valueQuantity = 45 'mm[Hg]'
```

When manual slice ordering is enabled, rules that set values on the `systolicBP` or `diastolicBP` slices _must_ use the slice name. With this option enabled, the `ExampleByName` instance would produce the same entries in `component`, but the `ExampleByNumber` instance would contain four entries in the `component` list: two entries that are not part of a named slice, followed by the `systolicBP` slice, and finally the `diastolicBP` slice.

When manual slice ordering is enabled, if any required slices with required assigned values are not present in an instance's list of rules, they will be added in the order in which they are defined. If you want these slices to appear in a different order on the instance without adding any new information to the slices, add rules on the instance that reassign the existing values:

```
Alias: $ObservationCategoryCodes = http://terminology.hl7.org/CodeSystem/observation-category

Profile: ExampleObservation
Parent: Observation
// slicing rules for category omitted for brevity
* category contains CategoryA 1..1 and CategoryB 1..1
* category[CategoryA] = $ObservationCategoryCodes#vital-signs
* category[CategoryB] = $ObservationCategoryCodes#survey

Instance: ReorderedExample
InstanceOf: ExampleObservation
// some required elements omitted for brevity
* category[CategoryB] = $ObservationCategoryCodes#survey
* category[CategoryA] = $ObservationCategoryCodes#vital-signs
```
This instance's `category` element will have the `survey` code as the first entry and the `vital-signs` code as the second entry.

Soft index resolution will account for named slices that have been referenced in previous rules:
```
Instance: SoftIndexExample
InstanceOf: ExampleObservation
// some required elements omitted for brevity
* category[+] = $ObservationCategoryCodes#laboratory // this + will resolve to index 0
* category[CategoryB] = $ObservationCategoryCodes#survey
* category[+] = $ObservationCategoryCodes#exam // this + will resolve to index 2, since the CategoryB slice occupies index 1
* category[CategoryA] = $ObservationCategoryCodes#vital-signs
```
This instance's `category` element will have four entries in the order specified: `laboratory`, `survey`, `exam`, `vital-signs`.

## Link References

{{% small-pageinfo color="primary" %}}
<span class="tag">SUSHI 3.0</span>Link references are only available in SUSHI 3.0.0 and later.
{{% /small-pageinfo %}}

SUSHI creates the **fsh-generated/includes/fsh-link-references.md** file to make it easier to create links to resource definitions in other markdown pages. This file's contents are a list of markdown link definitions, with one link for each resource in your **ImplementationGuide.json** file. This will include resources defined in FSH, the `resources` configuration property, and predefined resources. For example:
```markdown
[MyPatient]: StructureDefinition-MyPatient.html
[MyExtension]: StructureDefinition-MyExtension.html
```

The rules for determining what the link text will be for a given resource are as follows:
* For resources defined in FSH:
  * Non-instance resources use the _name_ of the resource.
  * Instances use the _id_ of the resource.
* For predefined resources:
  * Resources in the `input/examples` folder use the _id_ of the resource.
  * Resources in other sub-folders of `input` attempt to use the _name_ of the resource if this is a string value, and otherwise use the _id_ of the resource.
* For resources that are manually configured in `sushi-config.yaml`:
  * The _name_ of the resource is used, if available.
  * Otherwise, the _id_ of the resource is used.

To use these generated links, include `fsh-generated-links.md` in your custom markdown pages. This can be done by including the following line at the bottom of your custom markdown page:
```markdown
{% include fsh-link-references.md %}
```

Then, you can create links by including the resource's link text within square brackets. For example, if you had a Profile named `MyPatient`, your custom markdown file could look like this:
```markdown
## Patients
This IG provides [MyPatient] for patient information.

{% include fsh-link-references.md %}
```
