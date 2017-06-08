+++
categories = ""
date = "2017-04-24T00:00:00Z"
description = "Download FeatherGUI"
tags = []
title="Download FeatherGUI"

+++

{{< aside >}}
<h6>Featured Downloads</h6>

<div><b>Windows <i>(7 or later)</i></b>
<br/><code>SDK v{{< featherversion >}}</code>
<br/><a href="https://github.com/Black-Sphere-Studios/feathergui/releases/download/v{{< featherversion >}}/feathergui-windows-{{< featherversion >}}.zip">feathergui-windows-{{< featherversion >}}.zip</a>
</div>
<div><b>POSIX compatible systems</b>
<br/><code>SDK v{{< featherversion >}}</code>
<br/><a href="https://github.com/Black-Sphere-Studios/feathergui/releases/download/v{{< featherversion >}}/feathergui-posix-{{< featherversion >}}.zip">feathergui-posix-{{< featherversion >}}.zip</a>
</div>
{{</ aside >}}

### Download FeatherGUI v{{< featherversion >}}

SDKs for Windows and POSIX-compatible systems are available. An archive of old releases is available [here](https://github.com/Black-Sphere-Studios/feathergui/releases).

More details about the current release (v{{< featherversion >}}), along with a source code snapshot, can be found [here](https://github.com/Black-Sphere-Studios/feathergui/releases/tag/v{{< featherversion >}}).

You can also [clone the source code directly](https://github.com/Black-Sphere-Studios/feathergui), but this will not include prerequisites or any precompiled assets, so you will need to follow the [compilation instructions below](#compiling).

### Compiling FeatherGUI

If you are trying to build FeatherGUI directly from the source tree, you will need the [bss-util]() headers in the `include` directory (they should be inside `include/bss-util`). In addition, to avoid cluttering the source tree with binary files, the editor's icons are not included. If you are compiling the editor directly from source, you will still need to download the [most recent SDK](https://github.com/Black-Sphere-Studios/feathergui/releases/tag/v{{< featherversion >}}) and get the editor icons from there (you'll need `media/editor/16/` and `media/editor/controls`).

If you've downloaded the SDK, it includes all of the prerequisites and binary files. On Windows, open the solution file with Visual Studio and compile the project. On Linux, simply type `make` in a terminal open to the root folder to compile.

The windows-specific `fgDotNet` and `fgDirect2D` projects are dependent on the Windows SDK, which must be at least version **7.1** for `fgDirect2D`, which relies on `Direct2D 1.1`, and version **7.0** for `fgDotNet`, which current targets the `.net Framework 4.5`. However, the project files currently target a specific windows SDK version. If this version does not match the SDK you have installed, you can simply change the Target SDK Version number to the one you do have installed.

### Platform-Specific Projects
* **fgDirect2D**: Only works on Windows 7 SP1 and up
* **fgDotNet**: Only works on Windows 7 and up
* **fgExample-cs**: Only works on Windows 7 and up
* **fgNative**: Nonfunctional - **do not use**.