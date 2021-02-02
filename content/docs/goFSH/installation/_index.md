---
title: "Installation"
weight: 5
---

## Step 1: Install Node.js

GoFSH requires Node.js. To install Node.js, go to [https://nodejs.org/](https://nodejs.org/) and select the "LTS" download. If the download is not appropriate for your operating system, click the "Other Downloads" link to get a full list of downloads. Once the installer is downloaded, run it using the default options.

Ensure that Node.js is correctly installed by opening a command window and typing the following two commands. Each command should return a version number.

```shell
{{< terminal >}} node --version
{{< terminal >}} npm --version
```

## Step 2: Install GoFSH

To install GoFSH, open up a command prompt and type the following command:

```shell
{{< terminal >}} npm install -g gofsh
```

Check the installation via the command below:

```shell
{{< terminal >}} gofsh --help
```

If the command outputs instructions on using the GoFSH command line interface (CLI), you're ready to run GoFSH.

Use `gofsh -v` to display the installed version of GoFSH and the version of the FSH specification it supports. GoFSH follows the [semantic versioning](https://semver.org) convention (MAJOR.MINOR.PATCH):

* **MAJOR**: A major release has significant new functionality and, potentially, grammar changes or other non-backward-compatible changes.
* **MINOR**: Contains new or modified features, while maintaining backwards compatibility within the major version.
* **PATCH**: Contains minor updates and bug fixes, while maintaining backwards compatibility within the major version.


## Updating or Reverting GoFSH

To update GoFSH to the latest version, re-run:

```shell
{{< terminal >}} npm install -g gofsh
```

To revert to a previous version of GoFSH, run:

```shell
{{< terminal >}} npm install -g gofsh@{version}
```

where the `{version}` is replaced by the desired MAJOR.MINOR.PATCH version (e.g., `npm install -g gofsh@0.1.0`).
