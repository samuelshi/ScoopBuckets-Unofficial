# About [S4Utils](S4Utils.psm1)

_A PowerShell Script Module for this bucket, which contains several functions to help building app manifests._

## Functions

- [New-ProfileModifier](#new-profilemodifier)
- [Add-ProfileContent](#add-profilecontent)
- [Remove-ProfileContent](#remove-profilecontent)
- [Mount-ExternalRuntimeData](#mount-externalruntimedata)
- [Dismount-ExternalRuntimeData](#dismount-externalruntimedata)
- [Import-PersistItem](#import-persistitem)
- [New-PersistItem](#new-persistitem)
- [Backup-PersistItem](#backup-persistitem)

----

### `New-ProfileModifier`

_Generate scripts which modifies PowerShell profile._

|Parameters|Type|Mandatory|Descriptions|
|----|:----:|:----:|----|
|`Behavior`|String|&check;|Type of scripts to generate, support `ImportModule`, `RemoveModule` in current version.|
|`PSModuleName`|String|&check;|Name of PowerShell module, should be `$manifest.psmodule.name` in most situations.|
|`AppDir`|String|&check;|Path of the app directory, should be `$dir` in most situations.|
|`BucketDir`|String|&check;|Path of Scoop4kariiin bucket root directory.|

- See [Windows-screenFetch manifest](../bucket/Windows-screenFetch.json) for example.

----

### `Add-ProfileContent`

_Add certain content to PowerShell profile. Scripts generated by `New-ProfileModifier` will call this function._

|Parameters|Type|Mandatory|Descriptions|
|----|:----:|:----:|----|
|`Content`|String|&check;|Content to be added.|

- See [Windows-screenFetch manifest](../bucket/Windows-screenFetch.json) for example.

----

### `Remove-ProfileContent`

_Remove certain content from PowerShell profile. Scripts generated by `New-ProfileModifier` will call this function._

|Parameters|Type|Mandatory|Descriptions|
|----|:----:|:----:|----|
|`Content`|String|&check;|Content to be removed.|

- See [Windows-screenFetch manifest](../bucket/Windows-screenFetch.json) for example.

----

### `Mount-ExternalRuntimeData`

_Mount external runtime data._

|Parameters|Type|Mandatory|Descriptions|
|----|:----:|:----:|----|
|`Source`|String|&check;|Path of source folder in scoop persist directory.|
|`Target`|String|&cross;|The actual path which app uses to access the runtime data.|
|`AppData`|Switch|&cross;|Conveniently mount folder in `$env:APPDATA` by the name of source folder. Value of `$Target` will be overwritten.|

- Either `Target` or `AppData` should be specified.
- See [link-plus manifest](../bucket/link-plus.json) for example.

----

### `Dismount-ExternalRuntimeData`

_Unmount external runtime data._

|Parameters|Type|Mandatory|Descriptions|
|----|:----:|:----:|----|
|`Target`|String|&check;|Path or name of runtime folder mounted by scoop.
|`AppData`|Switch|&cross;|Conveniently dismount folder in `$env:APPDATA` with folder name in `Target` parameter. Value of `$Target` will be overwritten.|

- See [link-plus manifest](../bucket/link-plus.json) for example.

----

### `Import-PersistItem`

_Import files persisted by other app._

|Parameters|Type|Mandatory|Descriptions|
|----|:----:|:----:|----|
|`PersistDir`|String|&check;|Path of persist directory. Use `$persist_dir` here.|
|`SourceApp`|String|&check;|Name of source app to import from.|
|`ConflictAction`|String|&cross;|Actions when item conflicts.<br/>Use `Skip` to skip entire importing process.<br/>Use `Mix` to skip conflict items in target directory and import non-conflict ones from source directory.<br/>Use `Overwrite` to force import items from source directory and keep non-conflict ones in target directory.<br/>Use `ReplaceDir` to totally replace entire directory.|
|`Select`|String|&cross;|Specific items to import. Use `,` to separate multiple values.|
|`Sync`|Switch|&cross;|Create junction instead of copying files.|
|`Backup`|Switch|&cross;|Rename original item instead of removing it.|

- See [Snipaste2 manifest](../bucket/Snipaste2.json) for example.

----

### `New-PersistItem`

_Create items in persist directory._

|Parameters|Type|Mandatory|Descriptions|
|----|:----:|:----:|----|
|`PersistDir`|String|&check;|Path of persist directory. Use `$persist_dir` here.|
|`Name`|String|&check;|Name of item to create. Use `,` to separate multiple values.|
|`Type`|String|&check;|Type of item to create.|
|`Content`|String|&cross;|Initial content of file, use with parameter `-Type File`.|
|`Force`|Switch|&cross;|Force overwrite if target exists.|
|`Backup`|Switch|&cross;|Rename original item instead of removing it, use with parameter `-Force`.|

- See [Snipaste2 manifest](../bucket/Snipaste2.json) for example.

----

### `Backup-PersistItem`

_Backup items to persist directory._

|Parameters|Type|Mandatory|Descriptions|
|----|:----:|:----:|----|
|`AppDir`|String|&check;|Path of app directory. Use `$dir` here.|
|`PersistDir`|String|&check;|Path of persist directory. Use `$persist_dir` here.|
|`Name`|String|&check;|Name of item to backup. Use `,` to separate multiple values.|

- See [Snipaste2 manifest](../bucket/Snipaste2.json) for example.

----

## Use in manifests

```PowerShell
# Get the path of S4Utils.
# Find-BucketDirectory is a function added by scoop which helps generating bucket path.
# For Name parameter, use $bucket in installation-ish processes, use $install.bucket in uninstallation-ish processes.
$S4UtilsPath = Find-BucketDirectory -Root -Name $bucket | Join-Path -ChildPath "scripts\S4Utils.psm1"

# Import and use if exists.
if (Test-Path $S4UtilsPath) {
    Unblock-File $S4UtilsPath # Unblock file to avoid permission issues.
    Import-Module $S4UtilsPath

    ... # Call functions according to demands.

    Remove-Module -Name S4Utils -ErrorAction SilentlyContinue # Remove it to avoid conflicts.
}
```