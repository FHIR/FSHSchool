---
title: "GoFSH Tutorial"
weight: 20
resources:
- name: tutorial
  src: gofsh-tutorial.zip
  title: GoFSH Tutorial
- name: config
  src: sushi-config.yaml
  title: Configuration
---

This tutorial will walk you through an example of using [GoFSH](/docs/gofsh) to turn FHIR artifacts into FSH definitions. This tutorial assumes you have already installed [GoFSH](/docs/gofsh/installation).

### Step 1: Download Sample FHIR Artifacts

To start with some example FHIR artifacts, {{% download-file pre="download the" src="tutorial" %}} and unzip it into a directory of your choice.

After the file is unzipped, you should see two FHIR artifacts:

* **StructureDefinition-mcode-genetic-specimen.json**
* **StructureDefinition-mcode-laterality.json**

### Step 2: Run GoFSH

Now that you have GoFSH installed and sample FHIR artifacts, open up a command window, and navigate to the directory containing the artifacts you downloaded. Run GoFSH on those files by executing:

```shell
{{< terminal >}} gofsh .
```

{{% alert title="Note" color="primary" %}}
The dot (.) represents "this directory," the location of the files. You can also specify the location explicitly by replacing the dot with a directory path.
{{% /alert %}}

Running GoFSH will create a **gofsh** directory, and populate it with **input/fsh**, a directory containing several FSH files which define the original input files. If you open up **profiles.fsh**, you should see a FSH definition for a `Profile` called "GeneticSpecimen", and if you open up **extensions.fsh**, you should see an `Extension` called "Laterality".

### Step 3: Run SUSHI (Optional)

Now that you have generated FSH definitions, you can run SUSHI on those definitions to recreate your original input to GoFSH. First, ensure you have [SUSHI installed](/docs/sushi/installation). Then, navigate the command line to the **gofsh/** directory, and run:

```shell
{{< terminal >}} sushi .
```

This command will run SUSHI on the contents of **input/fsh**, and generate the output of that FSH into a directory called **fsh-generated/resources**. You can then compare the output from running GoFSH and then SUSHI to the original **StructureDefinition-mcode-genetic-specimen.json** and **StructureDefinition-mcode-laterality.json** files.

Alternatively, you can automatically run SUSHI on the output of GoFSH using the `-f` [option](/docs/gofsh/running/#fshing-trip). The following command:
```shell
{{< terminal >}} gofsh . -f
```
will run SUSHI on the output of GoFSH, and generate a comparison file in **gofsh/fshing-trip-comparison.html** which displays differences between the original input, and the output of SUSHI.