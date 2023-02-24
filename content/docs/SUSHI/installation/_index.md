---
title: "Installation"
weight: 5
description: >
  Instructions for installing the SUSHI tool on your system
---

## Step 1: Install Node.js

SUSHI requires Node.js. To install Node.js, go to [https://nodejs.org/](https://nodejs.org/) and select the "LTS" download. If the download is not appropriate for your operating system, click the "Other Downloads" link to get a full list of downloads. Once the installer is downloaded, run it using the default options.

Ensure that Node.js is correctly installed by opening a command window and typing the following two commands. Each command should return a version number.

```shell
{{< terminal >}} node --version
{{< terminal >}} npm --version
```

## Step 2: Install SUSHI

To install SUSHI, open up a command prompt and type the following command:

```shell
{{< terminal >}} npm install -g fsh-sushi
```

Check the installation via the command below:

```shell
{{< terminal >}} sushi --help
```

{{% small-pageinfo color="primary" %}}
<span class="tag">SUSHI 3.0</span>For SUSHI 3.0.0 and later, use `sushi help`.
{{% /small-pageinfo %}}

If the command outputs instructions on using the SUSHI command line interface (CLI), you're ready to run SUSHI.

Use `sushi -v` to display the installed version of SUSHI and the version of the FSH specification it supports. SUSHI follows the [semantic versioning](https://semver.org) convention (MAJOR.MINOR.PATCH):

* **MAJOR**: A major release has significant new functionality and, potentially, grammar changes or other non-backward-compatible changes.
* **MINOR**: Contains new or modified features, while maintaining backwards compatibility within the major version.
* **PATCH**: Contains minor updates and bug fixes, while maintaining backwards compatibility within the major version.

{{% alert title="Tip" color="success" %}}
For the most up-to-date information and latest releases of SUSHI, check the [release history and release notes](https://github.com/FHIR/sushi/releases).
{{% /alert %}}

## Reverting SUSHI

To revert to a previous version of SUSHI, run:

```shell
{{< terminal >}} npm install -g fsh-sushi@{version}
```

where the `{version}` is replaced by the desired MAJOR.MINOR.PATCH version (e.g., `npm install -g fsh-sushi@2.2.6`).
