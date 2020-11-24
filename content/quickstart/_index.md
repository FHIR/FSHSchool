---
title: Quick Start
menu:
  main:
    weight: 10
---
{{ define "main" }}

<a class="td-offset-anchor"></a>
<section class="row td-box td-box--1 position-relative td-box--gradient td-box--height-auto">
	<div class="container text-center td-arrow-down">
		<span class="h4 mb-0">
<h1>Quick Start</h1>

<p class = "lead mt-3">Get started producing FHIR Profiles and Implementation Guides in no time.</p></span>
	</div>
</section>

{{% blocks/section type="section" color="white"%}}

FHIR Shorthand (FSH) is a domain-specific language for defining the contents of FHIR Implementation Guides (IG). FSH can be created and updated using any text editor. Because it is text, it enables distributed, team-based development using source code control tools such as GitHub. 

**Here are three ways to get started:**

### 1. Do the Tutorials

The easiest way to get familiar with FSH is through the [hands-on tutorials](/docs/tutorials). The tutorials will show you the end-to-end process of creating an Implementation Guide (IG) using FSH and SUSHI (the FSH compiler).

### 2. Start a Project from Scratch

If you are starting a brand-new project, follow these steps:

1. [Install SUSHI](/docs/sushi/installation).

2. [Set up the directory structure](/docs/sushi/project/#initializing-a-sushi-project)  for your project.

3. Create a [sushi-config.yaml file](/docs/sushi/configuration/)

4. [Create FSH files](http://hl7.org/fhir/uv/shorthand/) containing your content.

5. [Run SUSHI](/docs/sushi/running) or the [IG Publisher tool](/docs/sushi/running/#running-the-ig-publisher) to create your FHIR artifacts and Implementation Guide.

### 3. Transform an Existing Implementation Guide

Using [GoFSH](/docs/gofsh), you can turn an existing FHIR Implementation Guide into a FSH project automatically. GoFSH instantly translates your StructureDefinitions, value sets, and examples into FHIR Shorthand.

{{% /blocks/section %}}
