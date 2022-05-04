---
title: "Plugins"
weight: 30
---

## Extending SUSHI with Plugins

Sometimes, your project may need to make modifications to SUSHI's behavior as it runs. To facilitate this, SUSHI provides a plugin system that allows author-provided code to execute at certain points as SUSHI runs. Plugins are configured on a per-project basis, allowing for different behavior for different projects.


## Warning Regarding Safe Use of Plugins

The plugin system provides the ability for arbitrary code to be run on your system. As with any software, you should only run plugins from sources you trust. The FSH team makes no guarantees about the safety of any plugin distributed by a third party.

## Overview of Plugins

The means by which the plugin system works is by loading the plugin at runtime and allowing the plugin to register functions to be called at certain points before or after a step in SUSHI's normal processing. These points are called "hooks." At each hook, SUSHI calls each function that has been registered at that hook. The arguments provided to the function vary depending on the hook.

SUSHI attempts to load each configured plugin after reading **sushi-config.yaml**. SUSHI will attempt to call a function named `initialize` exported by each plugin. The arguments passed to the `initialize` function are a function provided by SUSHI for registering at a hook, as well as any additional arguments listed in the [plugin configuration](#configuring-plugins).

The hooks that SUSHI provides are:
* before calling `fillTank`, which imports the FSH text and creates in-memory representations of the FSH definitions
* before and after calling `loadCustomResources`, which adds resources from supported resource directories and any additional directories specified with the `path-resource` subproperty of the `parameters` property in **sushi-config.yaml**.
* after calling `exportFHIR`, which converts in-memory FSH definitions into in-memory FHIR definitions.

The return values of registered functions are not used. Any error thrown by a registered function will be logged using SUSHI's normal logging mechanisms. Even if one registered function throws an error, the other functions at that hook will still be called.

## Writing Plugins

SUSHI is written in TypeScript, which transpiles to JavaScript before being run. Plugins, therefore, must also be in JavaScript in order to be loaded by SUSHI. SUSHI loads the plugin by calling `require` at the plugin's path. Then, SUSHI calls the `initialize` function provided by the plugin module. Therefore, the only required export from the plugin module is the `initialize` function.

### Initialize Function
A simple plugin could look something like this:
```javascript
function initialize(registerHook) {
  console.log('Hello from my plugin!');
}

module.exports = { initialize };
```

A plugin that takes extra arguments to its `initialize` function could look something like this:
```javascript
function initialize(registerHook, moreArguments, somethingElse) {
  console.log('Hello from my plugin!');
}

module.exports = { initialize };
```

The values of these extra arguments are provided in the [plugin configuration](#configuring-plugins) in **sushi-config.yaml**. Writing an initialize function to take extra arguments can allow for customization of how the plugin operates, such as by enabling optional features or by providing the path to an additional file resource.

To register functions, call `registerHook`, providing the name of the hook and the function to call. The four available hook names are:
* `beforeFillTank`
* `beforeLoadCustomResources`
* `afterLoadCustomResources`
* `afterExportFHIR`

### Using the `beforeFillTank` Hook
Functions registered at the `beforeFillTank` hook are called with three arguments: an array of [RawFSH](https://github.com/FHIR/sushi/blob/master/src/import/RawFSH.ts) objects, the [Configuration](https://github.com/FHIR/sushi/blob/master/src/fshtypes/Configuration.ts) being used by SUSHI, and the SUSHI [logger](https://github.com/FHIR/sushi/blob/master/src/utils/FSHLogger.ts). This hook is useful if you want to modify the FSH before it is parsed by SUSHI.

This plugin registers a function at the `beforeFillTank` hook:
```javascript
function initialize(registerHook) {
  registerHook('beforeFillTank', (rawFSH, configuration, logger) => {
    // Additional plugin logic here
  });
}

module.exports = { initialize };
```

### Using the `beforeLoadCustomResources` Hook

Functions registered at the `beforeLoadCustomResources` hook are called with five arguments: the path to the directory from where SUSHI will load custom resources, the path to the FSH project root directory, the [Configuration](https://github.com/FHIR/sushi/blob/master/src/fshtypes/Configuration.ts) being used by SUSHI, a [FHIRDefinitions] object that will contain the FHIR resources available to SUSHI during processing, and the SUSHI [logger](https://github.com/FHIR/sushi/blob/master/src/utils/FSHLogger.ts). When registered functions are called, the available FHIR definitions will include the core FHIR resources and any packages specified as dependencies in **sushi-config.yaml**. This hook is useful if you want to work with the FHIR dependencies or with the contents of the resource directories.

This plugin registers a function at the `beforeLoadCustomResources` hook:
```javascript
function initialize(registerHook) {
  registerHook('beforeLoadCustomResources', (resourceDir, projectDir, configuration, fhirDefinitions, logger) => {
    // Additional plugin logic here
  });
}

module.exports = { initialize };
```

### Using the `afterLoadCustomResources` Hook

Functions registered at the `afterLoadCustomResources` hook are called with five arguments: the path to the directory from where SUSHI will load custom resources, the path to the FSH project root directory, the [Configuration](https://github.com/FHIR/sushi/blob/master/src/fshtypes/Configuration.ts) being used by SUSHI, a [FHIRDefinitions](https://github.com/FHIR/sushi/blob/master/src/fhirdefs/FHIRDefinitions.ts) object that will contain the FHIR resources available to SUSHI during processing, and the SUSHI [logger](https://github.com/FHIR/sushi/blob/master/src/utils/FSHLogger.ts). These are the same arguments as used by functions registered at the `beforeLoadCustomResources` hook, but because these functions are called after loading custom resources, the FHIR definitions will also contain custom resources. This hook is useful if you want to work with the set of FHIR definitions that will be available to SUSHI during processing.

This plugin registers a function at the `afterLoadCustomResources` hook:
```javascript
function initialize(registerHook) {
  registerHook('afterLoadCustomResources', (resourceDir, projectDir, configuration, fhirDefinitions, logger) => {
    // Additional plugin logic here
  });
}

module.exports = { initialize };
```

### Using the `afterExportFHIR` Hook

Functions registered at the `afterExportFHIR` hook are called with three arguments: a [Package](https://github.com/FHIR/sushi/blob/master/src/export/Package.ts) of FHIR definitions created from FSH definitions, a [FSHTank](https://github.com/FHIR/sushi/blob/master/src/import/FSHTank.ts) containing each FSH definition, a [FHIRDefinitions](https://github.com/FHIR/sushi/blob/master/src/fhirdefs/FHIRDefinitions.ts) object containing all of FHIR resources that were available to SUSHI, and the SUSHI [logger](https://github.com/FHIR/sushi/blob/master/src/utils/FSHLogger.ts). This hook is useful if you want to work with the FHIR definitions produced by SUSHI.


This plugin registers a function at the `afterExportFHIR` hook:
```javascript
function initialize(registerHook) {
  registerHook('afterExportFHIR', (resourcePackage, fshTank, fhirDefinitions, logger) => {
    // Additional plugin logic here
  });
}

module.exports = { initialize };
```

### Implementation Notes

A plugin may register any number of functions at any or all of the hooks. Although all of the examples use unnamed arrow functions when registering, using named functions is also allowed. It is not necessary to export any of these registered functions. The `initialize` function, as well as any functions registered at hooks, may be synchronous or asynchronous.

### Making Your Plugin Loadable

For SUSHI to load a plugin, place it within the `sushi-plugins/{plugin-name}/` directory at the project root. The `plugin-name` is the name that will be used in **sushi-config.yaml** to tell SUSHI to load the plugin. When SUSHI loads the plugin, it will attempt to load the module at the `sushi-plugins/{plugin-name}/`path. Therefore, for a simple plugin that is only one file, the most straightforward way to ensure it is loaded correctly is for all of the code to be in `index.js` within the plugin's directory. For example, for a plugin named `my-fishy-plugin`, the plugin implementation would be at `sushi-plugins/my-fishy-plugin/index.js`. It is permissible for `index.js` to load other files that are part of your plugin. If you want the plugin's entry point to be a file other than `index.js`, a `package.json` file can be added to the plugin's directory that specifies the main file of the module. See the [NodeJS documentation](https://nodejs.dev/learn/the-package-json-guide) for more information about the `package.json` file.

## Configuring Plugins

SUSHI will only attempt to load plugins that are listed in **sushi-config.yaml**. The `plugins` configuration setting is provided to allow you to specify which plugins to use and any extra arguments to provide to them during initialization. Each plugin to be loaded should have its name listed. If the plugin should be installed from [npm](https://www.npmjs.com/), a version number should be included. If the plugin's `initialize` function requires additional arguments, they are included here. An example of what plugin configuration can look like follows:

```yaml
# sushi-config.yaml
plugins:
  - tuna-plugin 
  - add-herring: 3.0.1
  - more-trout:
      args:
        - 500
        - true
  - bigger-carp:
      version: 2.1.0
      args:
        - kilograms
        - 15
```

The above would add four plugins to be loaded by SUSHI.
* `tuna-plugin` has a name, but no other information. This type of configuration is useful when the plugin is managed directly by the author placing it at the project's `sushi-plugins/tuna-plugin/` directory.
* `add-herring` has a name and a version. SUSHI will install this plugin from npm. The plugin will be installed to `sushi-plugins/node_modules/add-herring/`.
* `more-trout` has a name and a list of arguments. Since there is no version, the plugin should be placed by the author at the project's `sushi-plugins/more-trout/` directory. The plugin's `initialize` function will receive three arguments: the registration function, the number `500`, and the boolean value `true`.
* `bigger-carp` has a name, a version, and a list of arguments. SUSHI will install this plugin from npm, and the additional arguments will be passed to the `initialize` function. The plugin will be installed to `sushi-plugins/node_modules/bigger-carp/`.