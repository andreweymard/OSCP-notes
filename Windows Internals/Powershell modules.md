### [Module](obsidian://open?vault=HTB-OSCP&file=Windows%20Internals%2FInteracting%20with%20Windows%2FPowershell) Components

A module is made up of `four` essential components:

1. A `directory` containing all the required files and content, saved somewhere within `$env:PSModulePath`.
	- This is done so that when you attempt to import it into your PowerShell session or Profile, it can be automatically found instead of having to specify where it is.

2. A `manifest` file listing all files and pertinent information about the module and its function.
	- This could include associated scripts, dependencies, the author, example usage, etc.

3. Some code file - usually either a PowerShell script (`.ps1`) or a (`.psm1`) module file that contains our script functions and other information.
    
4. Other resources the module needs, such as help files, scripts, and other supporting documents.

We could have our module be just a `*`.[[Powershell#PowerShell Extensions|psm1]] file that contains our scripts and context, skipping the manifest and other helper files. PowerShell would be able to interpret and understand what to do in either instance.

### Module Manifest

A module manifest is a simple `.psd1` file that contains a hash table. The keys and values in the hash table perform the following functions:

- Describe the `contents` and `attributes` of the module.
- Define the `prerequisites`. ( specific modules from outside the module itself, variables, functions, etc.)
- Determine how the `components` are `processed`.

If you add a manifest file to the module folder, you can reference multiple files as a single unit by referencing the manifest. The `manifest` describes the following information:

- `Metadata` about the module, such as the module version number, the author, and the description.
- `Prerequisites` needed to import the module, such as the Windows PowerShell version, the common language runtime (CLR) version, and the required modules.
- `Processing` directives, such as the scripts, formats, and types to process.
- `Restrictions` on the module members to export, such as the aliases, functions, variables, and cmdlets to export.

We can quickly create a manifest file by utilizing `New-ModuleManifest` and specifying where we want it placed.

#### Scope Levels

|**Scope**|**Description**|
|---|---|
|Global|This is the default scope level for PowerShell. It affects all objects that exist when PowerShell starts, or a new session is opened. Any variables, aliases, functions, and anything you specify in your PowerShell profile will be created in the Global scope.|
|Local|This is the current scope you are operating in. This could be any of the default scopes or child scopes that are made.|
|Script|This is a temporary scope that applies to any scripts being run. It only applies to the script and its contents. Other scripts and anything outside of it will not know it exists. To the script, Its scope is the local scope.|