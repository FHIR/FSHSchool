---
title: "GoFSH Tutorial"
weight: 20
resources:
- name: tutorial
  src: gofsh-tutorial.zip
  title: GoFSH Tutorial
- name: config
  src: config.yaml
  title: Configuration
---

This tutorial will walk you through an example of using [GoFSH](/docs/gofsh) to turn FHIR artifacts into FSH definitions. This tutorial assumes you have already installed [GoFSH](/docs/gofsh/installation).

### Step 1: Download Sample FHIR Artifacts

To start with some example FHIR artifacts, {{% download-file pre="download the" src="tutorial" %}} and unzip it into a directory of your choice.

After the file is unzipped, you should see two FHIR artifacts:

* **StructureDefinition-mcode-genetic-specimen.json**
* **StructureDefinition-mcode-laterality.json**

### Step 4: Run GoFSH

Now that you have GoFSH installed and sample FHIR artifacts, open up a command window, and navigate to the directory containing the artifacts you downloaded. Run GoFSH on those files by executing:

```shell
{{< terminal >}} gofsh .
```

{{% alert title="Note" color="primary" %}}
The dot (.) represents "this directory," the location of the files. You can also specify the location explicitly by replacing the dot with a directory path.
{{% /alert %}}

Running GoFSH will create a **fsh/** directory, and populate it with **resources.fsh**, a file containing the FSH definitions corresponding to the input files. If you open up **resources.fsh**, you should see two FSH definitions, a `Profile` called "GeneticSpecimen", and an `Extension` called "Laterality".

### Step 5: Run SUSHI (Optional)

Now that you have generated FSH definitions, you can run SUSHI on those definitions to recreate your original input to GoFSH. First, ensure you have [SUSHI installed](/docs/sushi/installation), then download the **config.yaml** file given below:
{{% show-file src="config" download="bottom" %}}

and place that file into the **fsh/** output directory that was created by GoFSH. Then, navigate the command line to the **fsh/** directory, and run:

```shell
{{< terminal >}} sushi .
```

This command will run SUSHI on **resources.fsh**, and generated the output into a directory called **build/**. You can then compare the output from running GoFSH and then SUSHI to the original **StructureDefinition-mcode-genetic-specimen.json** and **StructureDefinition-mcode-laterality.json** files.