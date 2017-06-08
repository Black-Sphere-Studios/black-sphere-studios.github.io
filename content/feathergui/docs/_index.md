+++
categories = ["feathergui", "Docs"]
date = "2017-02-27T00:00:00Z"
description = "FeatherGUI Official Documentation"
nav = ["Feather GUI"]
tags = []
title = "FeatherGUI Documentation"

+++
This serves as documentation for the C/C++ API of FeatherGUI - bindings to other languages have their own documentation and may introduce their own use constraints depending on the needs of the language. While FeatherGUI is built around a core C API, the C++ API will also be mentioned to provide a quick way to visualize the accepted types and default arguments that the C API cannot expose.

All FeatherGUI controls are built on top of the [Element]({{< ref "feathergui/docs/Element.md" >}}), which defines the default behavior for all messages and the basic layout structure. Basic data structures and helper functions can be found in the [Feather]({{< ref "feathergui/docs/Feather.md" >}}) section. An explanation of the backend functions, the provided default backend functions, and the requirements when building a proper backend can be found in the [Backend]({{< ref "feathergui/docs/Backend.md" >}}) section. All other controls can be found in their respective section.

##### Rendering Model
{{< image src="/img/layout_diagram1.svg" align="right" width="337">}}
At it's core, FeatherGUI uses a unified coordinate system, with padding, margins, centering, minimum and maximum dimensions. The unified coordinate system means that the area of any given control is specified via both *relative* and *absolute* coordinates. This **outer area** then applies the *margins* to get the **standard area**, which then applies the *padding* to acquire the **inner area**. The **outer area** cannot ever exceed the maximum dimensions, if they are specified, nor can it shrink farther than the minimum dimensions.


For a detailed overview of the layout system, see [Layout System]({{< ref "feathergui/docs/Layout-System.md" >}}). FeatherGUI elements can have any number of children, which are divided into **foreground** and **background** elements. **Foreground** elements are specified relative to the **inner area**, while the **background** elements are specified relative to the **standard area**. The core `Element` structure supports an **arbitrary layout function**, which allows it to re-arrange it's **foreground** children however it pleases. The [Box]({{< ref "feathergui/docs/Box.md" >}}) control provides a powerful built-in tiling layout function, but anyone can provide their own [custom layout function]({{< relref "feathergui/docs/Layout-System.md#Custom" >}}).

##### Messaging System
While internally FeatherGUI uses C++, it is built around the raw C interface that it exposes. All of FeatherGUI is predicated on <i>single-inheritance</i>, which means the inherited class is simply embedded as the first element of the subclass. This means that all featherGUI controls can safely be cast in c-style via `(fgElement*)`, and this will always be valid. Only four virtual function pointers are used: the `message()` function, the `destroy()` function, and the `free()` function. All other functions, including the constructor, go through the `message()` function, which allows a subclass to "override" a function by providing it's own `message()` function that performs some action in response to a message before it reaches the base class.

The message processing function has a simple signature: `size_t fgMessage(fgElement* self, const FG_Msg* msg);`. The `self` parameter is whatever object is recieving the message (and is generally of whatever type that object is). The `FG_Msg` struct contains the message type and arguments, and the `size_t` is a pointer-sized return argument. A return value of **zero** means the message has been ***rejected*** (or that there was no valid action that could be done). A **non-zero** return value means the message has been accepted, and may optionally have returned some value, which could be an integer, a float, or a pointer. The C++ API automatically casts the return value to the appropriate type, but if you are using the Raw C API, make sure you check the documentation so you know what return value to expect.

This is the FG_Msg struct:
{{<pre cpp>}}typedef struct _FG_MSG {
  fgMsgType type;
  fgMsgType subtype;
  union {
    struct { float x; float y; // Mouse and touch events
      union { 
        struct { unsigned char button; unsigned char allbtn; }; 
        struct { short scrolldelta; short scrollhdelta; }; // MOUSESCROLL
        short touchindex; // Touch events
      };
    }; 
    struct {  // Keys
        int keychar; //Only used by KEYCHAR, represents a utf32 character
        unsigned short keyraw; // In some cases (e.g. games) an application may want to pass around the raw hardware scancode of a key.
        unsigned char keycode; //only used by KEYDOWN/KEYUP, represents an actual keycode in FG_KEYS, not a character
        char sigkeys; // 1: shift, 2: ctrl, 4: alt, 8: held
    };
    struct { float joyvalue; short joyaxis; }; // JOYAXIS
    struct { char joydown; short joybutton; }; // JOYBUTTON
    struct {
      union { void* p; ptrdiff_t i; size_t u; FABS f; struct _FG_ELEMENT* e; };
      union { void* p2; ptrdiff_t i2; size_t u2; FABS f2; struct _FG_ELEMENT* e2; };
    };
  };
} FG_Msg;
{{</pre>}}

Mouse input, touch input, joystick input, and keystroke input have special, dedicated arguments in `FG_Msg`. All other messages use the two "generic" arguments specified at the bottom of the message. Both generic arguments can be a pointer, a pointer-sized signed integer, a pointer-sized unsigned integer, a floating point value of the width specified by `FABS`, or a pointer of type `fgElement*` (included for convenience). In addition, a message has both a `type` and a `subtype`. `enum FG_MSGTYPE` contains all built-in message types, but programs can define their own message types starting after `FG_CUSTOMEVENT`. The `subtype` is a value that gives additional type or behavior information to a message. For example, the `FG_VALUE` message accepts multiple types, and can provide a `FGVALUE_INT64` to signal that its argument should be interpreted as an integer, or `FGVALUE_FLOAT` if it should be interpreted as a float.

Certain events, like input events, are *injected* into the GUI tree. For mouse or touch events, the `x,y` location is used to determine which element recieves the message first. Children are always considered "above" their parents, and if they are capable of accepting injected events, will recieve them first. If a child *rejects* a message for whatever reason, the element immediately below it is given the message (this is usually the child's parent, but if the child is non-clipping, it could be a completely different element). This cycle repeats until the message reaches the **root element**, which can then eat the message or also reject it, depending on the backend implementation. Key presses and joystick input are simply sent to whichever control has focus, and works it's way up the parent elements until the message is accepted or it reaches the root element.