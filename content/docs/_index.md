---
title: "Documentation"
linkTitle: "Documentation"
menu:
  main:
    weight: 20
---


{{% alert title="Note" color="success" %}}
**Documentation of the FHIR Shorthand language standard is found on [the official HL7 site](http://hl7.org/fhir/uv/shorthand). The [Language Reference](http://hl7.org/fhir/uv/shorthand/reference.html) has details on FSH grammar. This site covers SUSHI and GoFSH and creation of FHIR Implementation Guides using these tools.**
{{% /alert %}}

### What is SUSHI?

SUSHI (**S**USHI **U**nshortens **Sh**ortHand **I**nputs) is a FSH compiler. SUSHI converts FSH language to FHIR artifacts. SUSHI can run in stand-alone mode or as part of the [HL7 IG Publisher](https://confluence.hl7.org/display/FHIR/IG+Publisher+Documentation).

### What is GoFSH?

GoFSH is a converter that takes FHIR artifacts (e.g., profiles, extensions, value sets, instances) and produces equivalent FSH. GoFSH is essentially the opposite of SUSHI. GoFSH helps you transition to FSH if you have an existing Implementation Guide produced by other methods.

### Navigating the Documentation

* Head to [Introduction](/docs/introduction) for some background information on FSH and FHIR.

* If you want to dive right into creating an Implementation Guide, check out the [Tutorials](/docs/tutorials) for hands-on exercises.

* If you're ready to start writing some FSH, but first want an overview, consult the latest presentations in [Downloads](/downloads).

* Go to [SUSHI](/docs/sushi) to learn about the SUSHI compiler for FSH. 

* If you already have an Implementation Guide and want to switch over to FSH, consult the [GoFSH documentation](docs/gofsh).

* If you are looking for specific information, you can search the documentation using the search box in the upper right corner.
