+++
author = "Erik McClure"
categories = ["feathergui", "News"]
date = "2017-06-04T01:06:00Z"
description = "The initial v0.1 developer preview of FeatherGUI is now available for testing. Please report any bugs to the Github Repo, and join the official discord server!"
image = "/img/feathergui_box.png"
largeimage = "/img/featherGUI-editor-large.png"
largeimagewidth = 800
layout = "article"
tags = ["news", "feathergui_news"]
title = "FeatherGUI v0.1 Released"

+++
The [**FeatherGUI v0.1.0 developer preview**](https://github.com/Black-Sphere-Studios/feathergui/releases/tag/v0.1.0) has been released, and is now available for testing! As the first public release of FeatherGUI, this is an **alpha** release, which is not considered stable and should not be used in production. Known bugs are [tracked on Github](https://github.com/Black-Sphere-Studios/feathergui/issues), but please report any new bugs by [filling a Github Issue](https://github.com/Black-Sphere-Studios/feathergui/issues/new). Join the [official discord server](https://discord.gg/nFczp8J) to discuss issues with the developer and talk to other users of FeatherGUI!

The SDK contains precompiled binaries, debug symbols, media files, and the [bss-util v0.5.0](https://github.com/Black-Sphere-Studios/bss-util/releases/tag/v0.5.0) include files that aren't included in the source tree to make compilation easier. Note, however, that compiling fgDirect2D and fgDotNet requires a compatible version of the Windows SDK installed and configured on your machine. If Visual Studio complains it can't find the SDK, you can try to change the target SDK version to the one you have - any version will do, so long as it supports Direct2D v1.1.

A layout editor is included in this SDK. While it is functional, there are a number of [bugs and missing features](https://github.com/Black-Sphere-Studios/feathergui/issues/33) due to this being an early alpha release.

A POSIX pre-compiled SDK is also provided, but there are currently no POSIX-compatible backends, so while both FeatherGUI and fgExample-c compile in POSIX environments, they are useless without a backend to support them (the example will segfault without a backend).

[**FeatherGUI Download Page**]({{< ref "feathergui/get.md" >}})