---
title: "Visual Studio Code Extension"
weight: 25
---

Documentation for the FHIR Shorthand language extension for Visual Studio Code (VS Code) can be (found here)[https://github.com/standardhealth/vscode-language-fsh#readme]. Once installed in VS Code, the documentation can be accessed by clicking on the installed FHIR Shorthand extension (look for the fish icon on the Extensions pane on the left navigation bar).

The VS Code extension offers the following features:

* **Syntax highlighting.** Colorizing FSH text allows easier reading and writing of FHIR Shorthand.
* **Autocomplete.** This feature makes contextually appropriate suggestions after when specifying a nested path, the `Parent` keyword, and when writing `obeys` rules.
* **Tasks.** The extension allows you to run SUSHI and check your work inside the VS Code environment. SUSHI's log messages go to the integrated Terminal tab, and errors go to VS Code's Problems tab. Selecting an error or warning will navigate directly to the line or area of code responsible for the error or warning.
* **Go to Definition.** This feature allows quick navigation to the definition of an item, anywhere the name of the item is used. If it is a native FHIR item, the link to the documentation page is offered.
* **Snippets.** This feature automatically adds relevant keywords and placeholders so you can easily enter all of the recommended metadata for a FSH definition.