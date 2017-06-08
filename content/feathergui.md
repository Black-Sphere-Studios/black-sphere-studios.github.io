+++
author = "Erik McClure"
categories = ["feathergui", "Technology"]
date = "2017-04-27T00:00:00Z"
description = "FeatherGUI is the next step in the evolution of modern UI design, a universal GUI abstraction layer."
image = "/img/feathergui_box.png"
tags = []
title = "FeatherGUI Abstraction Layer"
type = "default"

+++
FeatherGUI is the next step in the evolution of modern UI design. It combines the lightweight flexibility of Nuklear with the native tooling support of Qt and the ease of use of HTML. FeatherGUI is not just another widget toolkit, it serves as an abstraction layer for the entire user interface, a layer *above* other UI libraries. By seperating the underlying UI logic from the layout and behavior, FeatherGUI delivers the first **truly language agnostic UI standard**.

Like Nuklear, you can write your own backend for FeatherGUI, making it easy to integrate into your game engine. However, like Qt, a backend for FeatherGUI can be implemented using **native operating system controls**, and can piggy-back on the operating system's own message pump. This is all controlled by a **standard binary layout format**, which can be serialized and parsed to the file format of your choice - an XML schema is already included. 

All this, combined with FeatherGUI's native C interface means that you no longer have to wait for your favorite language or operating system to be supported. FeatherGUI is completely platform-independent and language-agnostic, allowing you to implement a binding for your favorite language, or a backend for whatever platform you desire.