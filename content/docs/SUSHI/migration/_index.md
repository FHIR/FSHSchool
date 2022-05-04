---
title: "Migrating from Older Versions"
linkTitle: "Migration"
weight: 40
menu:
  main:
    weight: 60
---

## Migrating from pre-1.0.0 Projects

The SUSHI project structure and configuration changed significantly between pre-1.0.0 versions of SUSHI and versions of SUSHI 1.0.0 or greater. This guide will assume you currently have a project structure similar to the **customized ig** shown in the [project structure documentation](/docs/sushi/project/#ig-projects) for pre-1.0.0 versions of SUSHI.

To migrate your project, follow these steps:

### 0 - Create a Backup

Before beginning the migration process, **make a copy of your entire project directory structure**, and keep it in a safe place.

### 1 - Delete SUSHI-Generated Files

If you have previously used SUSHI, you have SUSHI-generated files that need to be cleaned up. If you use Git, you can remove all untracked files using the command:

```shell
{{< terminal >}} git clean -xfd
```

Before executing this command, you can run `git clean -xfdn` to preview the list of files Git will remove. If it looks OK, then run the command above.

If you are not using Git, you can refer to **./SUSHI-GENERATED-FILES.md** for a partial list of files to delete. (This list does not include generated profiles, value sets, extensions, and examples found in the **./input** directory that should also be deleted.)

Keep in mind that if you created FHIR artifacts NOT using SUSHI, those files must be retained. Typically, they will be found under the **./input** directory.

### 2 - Rearrange Directories

Move the entire contents of **./fsh/ig-data** directory to the top level (do not move the **/ig-data** directory, just its contents).

{{% alert title="Note" color="primary" %}}
If after the cleanup, you still have a top-level **./input** directory, then _merge_ the contents of **./fsh/ig-data/input** into that directory (rather than replacing it).
{{% /alert %}}

Next, move the entire **./fsh** directory into the **./input** directory (resulting in a **./input/fsh** directory).

Finally, delete the **./input/fsh/ig-data** directory (which should be empty).

Your directory structure should now look something like this:

```text
.
└── input
│   ├── fsh
│   ├── images
│   └── pagecontent
└── (other directories such as input-cache, output, temp, template)
```

### 3 - Adjust Page Names or Links

SUSHI no longer removes numeric prefixes from the file names of pages. If you did not use numeric prefixes on page files you can skip to step 4. Otherwise, you will need to rename your page files or fix your existing links to these pages.

**3.1 Rename Page Files**

To maintain the same page names and locations in your IG, remove the numeric prefixes from your page file names.  For example, rename `3_downloads.md` to `downloads.md`. Then add a `pages` property to `sushi-config.yaml` to specify the desired page order in the table of contents. For example:
```yaml
pages:
  index.md:
    title: Home
  conformance.md:
    title: Conformance
  implementation.md:
    title: Implementation
  downloads.md:
    title: Downloads
```

**3.2 Fix Existing Links to Pages**

If you want to continue specifying page order using numeric prefixes on file names instead, you will need to fix all existing links to _include_ the numeric prefix.  For example, the link
```md
[Downloads](downloads.html)  (or <a href="downloads.html">Downloads</a>)
```
should become:
```md
[Downloads](3_downloads.html) (or <a href="3_downloads.html">Downloads</a>)
```

### 4 - Move and Rename config.yaml

Move the **./input/fsh/config.yaml** file to the top-level folder, and rename it **sushi-config.yaml**.

### 5 - Run SUSHI and Follow Instructions

You can now run SUSHI 1.0. You may get further instructions in the form of error messages.

{{% alert title="Note" color="primary" %}}
Although SUSHI 2.0 is available for use, if you are migrating from a pre-1.0.0 version of SUSHI, we recommend first updating to the latest SUSHI 1.0 version of SUSHI (this is [SUSHI 1.3.2](https://github.com/FHIR/sushi/releases/tag/v1.3.2) and can be installed via `npm install -g fsh-sushi@1.3.2`). After resolving all errors and warnings in that version, only then should you migrate to a SUSHI 2.0 release.
{{% /alert %}}


One error message concerns the `template` property in **sushi-config.yaml**. If you get that error, follow the instructions to remove that property from **sushi-config.yaml** and create an **./ig.ini** file.

{{% alert title="Warning" color="warning" %}}
**Carefully consider the contents of ig.ini**. You must specify a `template` property based on `fhir.base.template#current`. Older templates will not work with SUSHI 1.0. Any of the following should work:

  * `template = fhir.base.template#current`
  * `template = hl7.base.template#current`
  * `template = hl7.fhir.template#current`
  * `template = hl7.davinci.template#current`
  * `template = hl7.cda.template#current`

The `ig` property must point to an ImplementationGuide resource. If SUSHI is generating your ImplementationGuide resource, it will be generated to **fsh-generated/resources/ImplementationGuide-{ig-id}.json**.
{{% /alert %}}

Another error message you might receive concerns the `history` property in **sushi-config.yaml**. If you get that message, remove that property and create the **package-list.json** file as instructed.

When you get a clean build with SUSHI, try running the IG Publisher using your customary method (e.g., using the `_genonce` script). This will verify that all files needed by the IG Publisher are in the right place.

### 6 - Update .gitignore
If you are using Git for version control, update your **.gitignore** file.

* Remove entries for **ig.ini**, **package-list.json**, and the **input** directory, if present. These two files and the entire **./input** folder should now be under source code control.

* Add **./fsh-generated** to .gitignore. This is where SUSHI 1.0 puts all generated files.

Here is a typical **.gitignore** suitable for SUSHI 1.0:

```text
.DS_Store
Thumbs.db
/fsh-generated
/input-cache
/output
/template
/temp
```

### Troubleshooting

Some users may experience one of these issues when running the IG Publisher on their migrated project:

* The IG publisher reports `No Source directories to scan found`
* The published IG does not include any of the resources from **fsh-generated**

This usually means that the IG Publisher is not using the correct template.  _First_, check to ensure your your **ig.ini** specifies the `#current` version of one of the supported templates (or a template that extends one of the supported templates).

If you've confirmed you are using a `#current` version of a supported template, then the IG Publisher likely failed to download the updated template in your FHIR cache.  This may be due to corporate firewalls, network issues, or restrictive file permissions in your FHIR cache.  Sometimes, you can resolve this issue by deleting the base template from your FHIR cache:

&nbsp;&nbsp;&nbsp;&nbsp;{{< windows >}} `C:\Users\{user}\.fhir\packages\fhir.base.template#current`

&nbsp;&nbsp;&nbsp;&nbsp;{{< apple >}} `/Users/{user}/.fhir/packages/fhir.base.template#current`

substituting `{user}` with your username on your operating system.

{{% alert title="Note" color="primary" %}}
If there is a general problem with downloading the template (as opposed to overwriting it), deleting the template will not fix it. Instead the problem will just become more obvious. If this is the case, you may need to disconnect from your VPN (if applicable) and try again or contact your network administrator for assistance.

For this reason, you may choose to temporarily _move_ your **fhir.base.template#current** folder rather than _deleting_ it.  This way, if the new template still fails to download, you can move the old template back and continue using it for other IGs (for which the new template may not be necessary).
{{% /alert %}}
