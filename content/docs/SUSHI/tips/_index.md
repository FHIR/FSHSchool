---
title: "Tips and Tricks"
weight: 25
description: >
  Helpful tips and tricks that may be useful when running SUSHI on more complex FSH projects
---

## Specifying Additional Resource Paths

Sometimes authors may wish to put predefined resources in folders other than the normally supported `input` sub-folders. To support this, SUSHI now recognizes the [ImplementationGuide parameter](https://confluence.hl7.org/display/FHIR/Implementation+Guide+Parameters) `path-resource`. Authors can include this parameter in `sushi-config.yaml` to specify relative paths to additional folders that contain predefined resources. For example, the following can now be used in `sushi-config.yaml` to include resources from the sub-folder `predefined-resources`:

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
* item ^type.profile = http://example.org/StructureDefinition/MyQuestionnaire
* item ^type.profile.extension.url = http://hl7.org/fhir/StructureDefinition/elementdefinition-profile-element
* item ^type.profile.extension.valueString = "Questionnaire.item"
// ...
```

See the following documentation for additional details:
* [Extension: profile-element](https://www.hl7.org/fhir/extension-elementdefinition-profile-element.html) from the FHIR specification
* [Clarification on contentReference](https://chat.fhir.org/#narrow/stream/179252-IG-creation/topic/Clarification.20on.20contentReference/near/187852552) discussion on Zulip

## Instances of Logical Models

The IG Publisher supports including instances of logical models as binary resources. This feature was announced and discussed in a [Logical Model Examples](https://chat.fhir.org/#narrow/stream/179252-IG-creation/topic/Logical.20Model.20Examples/near/251192344) thread on chat.fhir.org.  The basic steps an author needs to take in order to include logical model examples in a SUSHI project are:

1. Add the example to the `input/resources` or `input/examples` folder
    a. The file name of the example should be `Binary-{id}.json` or `Binary-{id}.xml` (substituting `{id}` for the real id)
2. Add an entry for the example in the `sushi-config.yaml` `resources` property
    a. Specify a `name`
    b. Specify `exampleCanonical` pointing to the canonical of your logical model
    c. Add an extension w/ the proper resource format (`application/fhir+json` or `application/xml`)

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

This will result in your logical model example being listed and displayed as a proper example of the logical model.

{{% alert title="Note" color="success" %}}
This does not allow/support using the `Instance` keyword for creating examples of logical models. Authors must create the example as raw JSON or XML.  Support for the `Instance` keyword may come in future versions of SUSHI.
{{% /alert %}}

## Manual Slice Ordering

Starting in SUSHI `v3.0.0`, authors can exercise full manual control over the ordering of slice elements within Instances. Previous versions of SUSHI allowed for partial control of slice element ordering, but some ordering was determined by SUSHI's implementation and could not be affected by an author. In current version of SUSHI (`v3.0.0` or later), authors can configure their FSH projects to manually control slice ordering.

Manual slice ordering follows the following rules:

* slices appear on an Instance in the order of FSH rules
* any required slices (`1..*`) that are not referenced in a FSH rule on the Instance appear after all referenced slices in the order in which they are defined on the Instance's StructureDefinition (the instance's `InstanceOf`)
* a rule that references a sliced element must reference it using the slice name
  * Note: when manual slice ordering is enabled, it is not possible to refer to an element with a slice name by numeric index only. Without this option, if an author knew which numeric index a slice would be at in the exported instance, that element could be accessed without using the slice name.
  * e.g.: `* extension[my-slice].valueString = "hello"` must be used


To use this ordering, add the following to `sushi-config.yaml`:

```yaml
instanceOptions:
  manualSliceOrdering: true
```

### Examples

```
Profile: ExampleObservation
Parent: Observation
// slicing rules for component omitted for brevity
* component contains systolicBP 1..1 MS and diastolicBP 1..1 MS
```

When using this profile without manual slice ordering, the `systolicBP` slice will always be the first entry in the `component` element, and the `diastolicBP` slice will always be the second entry in the `component` element. So, the following two instances would have the same `component` elements:
```
Instance: ExampleByName
InstanceOf: ExampleObservation
// some required elements omitted for brevity
* component[systolicBP].valueQuantity = 108 'mm[Hg]'
* component[diastolicBP].valueQuantity = 45 'mm[Hg]'

Instance: ExampleByNumber
InstanceOf: ExampleObservation
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
// slicing rules for component omitted for brevity
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