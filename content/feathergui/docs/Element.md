+++
categories = ["feathergui", "Docs"]
date = "2017-02-27T00:00:00Z"
description = "FeatherGUI Official Documentation"
tags = ["fgElement.h", "feathergui_docs"]
title = "Element"
nav = ["Feather GUI", ]
layout = "doc"
+++

### Element
The Element serves as the base structure for all FeatherGUI controls. It is also where the C++ API defines all of it's functions, so in C++, other controls overload the `->` operator to automatically convert to the base `fgElement*` pointer for easy access to the API functions. This compensates for the fact that the C++ API cannot use proper inheritance without breaking the C API interop. `fgElement` provides the default implementation for a message, if there is any, otherwise it will reject the message by returning `0`. Some default implementations are empty and do nothing, but will instead return `FG_ACCEPT`, which evaluates to 1, which always accepts the message.

## Accepted Messages
{{<pre cpp>}}bool FG_CONSTRUCT(){{</pre>}}

`fgElement` performs all necessary setup before this message is called, so this does nothing, but always returns `FG_ACCEPT`.

{{<pre cpp>}}bool FG_MOVE[FGMOVE](fgElement* child, size_t){{</pre>}}

This message is sent in response to any potential resize or move event that could change the effective area of this element or its children. A child will propagate this event up to its immediate parent, but no farther (unless that parent chooses to propagate it farther). An element will always, however, propagate a move element <i>down</i>, unless it is determined that it would have no effect on the layout.

{{<pre cpp>}}bool FG_SETAREA[FGUNIT](const CRect* area){{</pre>}}

Sets the area of this element to the given area, applying the given `FGUNIT` flags sent in the subtype. This message triggers `FG_MOVE` only if the area actually changes. This message calls `fgDirtyElement` and `fgElement_MouseMoveCheck` **twice**, once before and one after the area is actually set. If the subtype is -1, an `FG_MOVE` is not actually sent to this element, but instead directly propagated to it's children.

{{<pre cpp>}}bool FG_SETTRANSFORM[FGUNIT](const fgTransform* transform){{</pre>}}

Sets the entire transform of this element to the given transform, applying the given `FGUNIT` flags sent in the subtype. First, `FG_AREA` is called with the area, and then the rotation and center are set afterwards. This message triggers `FG_MOVE` only if the transform actually changes. This message calls `fgDirtyElement` and `fgElement_MouseMoveCheck` **twice**, once before and one after the area is actually set. If the subtype is -1, an `FG_MOVE` is not actually sent to this element, but instead directly propagated to it's children.

{{<pre cpp>}}bool FG_SETFLAG(fgFlag flag, bool state){{</pre>}}

This message simply transforms a call to add or remove a flag into an `FG_SETFLAGS` message, by ORing or ANDing the flag with the current flag value.

{{<pre cpp>}}bool FG_SETFLAGS(fgFlag flags){{</pre>}}

Replaces the current flags with the given flags. This can trigger a number of events, depending on the flags changed. `BACKGROUND/EXPAND` will trigger a `LAYOUTCHANGE`, whereas `IGNORE/NOCLIP` will swap which sublist the child is being tracked on. `HIDDEN/NOCLIP` will call `fgDirtyElement()` on itself.

{{<pre cpp>}}bool FG_SETMARGIN[FGUNIT](const AbsRect* margin){{</pre>}}

Sets the margin of this element to the given margin, applying the given `FGUNIT` flags sent in the subtype. This message triggers `FG_MOVE` only if the margin actually changes. This message calls `fgDirtyElement` and `fgElement_MouseMoveCheck` **twice**, once before and one after the area is actually set. If the subtype is -1, an `FG_MOVE` is not actually sent to this element, but instead directly propagated to it's children.

{{<pre cpp>}}bool FG_SETPADDING[FGUNIT](const AbsRect* padding){{</pre>}}

Sets the padding of this element to the given padding, applying the given `FGUNIT` flags sent in the subtype. This message triggers `FG_MOVE` only if the padding actually changes. This message calls `fgDirtyElement` and `fgElement_MouseMoveCheck` **twice**, once before and one after the area is actually set. If the subtype is -1, an `FG_MOVE` is not actually sent to this element, but instead directly propagated to it's children.

{{<pre cpp>}}bool FG_SETPARENT[FGSETPARENT](fgElement* parent, fgElement* next){{</pre>}}

Sets the parent of this element to the given parent, inserting this element before the `next` element in the parent's list of children. If next is `NULL`, it is inserted *after* the last element.

{{<pre cpp>}}bool FG_REORDERCHILD(fgElement* child, fgElement* next){{</pre>}}

Reorders the child element without changing its' parent, inserting it before the `next` element in the parent's list of children. If next is `NULL`, it is inserted *after* the last element.

{{<pre cpp>}}bool FG_ADDITEM[FGITEM](fgElement*/const char*/void* item){{</pre>}}

If the subtype is nonzero, this message is rejected. Otherwise, it is passed through to `FG_ADDCHILD`.

{{<pre cpp>}}bool FG_ADDCHILD(fgElement* child, fgElement* next){{</pre>}}

Sets the child's parent to this element and inserts it before `next`. If next is `NULL`, inserts it after the last element.

{{<pre cpp>}}bool FG_REMOVECHILD(fgElement* child){{</pre>}}

Removes a child from this element. If the child isn't a member of this element, a debug assertion will fail.

{{<pre cpp>}}bool FG_LAYOUTCHANGE(fgElement* target, fgElement* old){{</pre>}}

Simply calls `FG_LAYOUTFUNCTION` with the appropriate arguments and processes the resulting area if either `EXPANDX` or `EXPANDY` flags are present.

{{<pre cpp>}}bool FG_LAYOUTFUNCTION[bool](const FG_Msg* msg, const CRect* area){{</pre>}}

Calls the default layout function. This should be overriden if you want to use a [custom layout function]({{< relref "feathergui/docs/Layout-System.md#Custom" >}}).

{{<pre cpp>}}fgElement* FG_LAYOUTLOAD(fgLayout* target){{</pre>}}

Loads the provided layout, applying any styles set on the layout root to this element, and then adding all elements inside the layout root to this element, recursively. The function returns a pointer the *last* element added from the layout, or `NULL` if the layout either could not be loaded or had no children.

{{<pre cpp>}}size_t FG_CLONE(fgElement* e){{</pre>}}

This function always returns the total size, in bytes, needed to clone this control, but if `e` is not `NULL`, then it will be assumed to point to a valid memory location that is at least as large as the return value of this function, and copy the contents of this element over to it, which includes calling `FG_CLONE` on all of it's children.

{{<pre cpp>}}const char* FG_GETCLASSNAME(){{</pre>}}

This message always returns a string containing the class name, which in this case is "Element". It should be overriden by all controls and set to return a string containing the class name of that control.

{{<pre cpp>}}fgSkin* FG_GETSKIN(fgElement* child){{</pre>}}

If child is `NULL`, this simply returns whatever the Element's currently assigned skin is (or `NULL` if it doesn't have one). Otherwise, it performs a skin lookup for the child by going through it's skin (if it has one) and trying to find a match first for the child's name, and then for the child's class name. If all of these fail (or it has no skin), the element calls `FG_GETSKIN` on it's parent, passing the child parameter through. This continues until it reaches the root element or succeeds. If no skin can be found, returns `NULL`.

{{<pre cpp>}}bool FG_SETSKIN(fgSkin* skin){{</pre>}}

Manually sets the skin to the provided skin. If the provided skin is `NULL`, then the element will ask it's parent to find an appropriate skin. If the resulting skin actually changes, it will reset the skin for all of it's children (which will overwrite any manually assigned skins).

{{<pre cpp>}}bool FG_SETSTYLE[FGSETSTYLE](fgStyle*/FG_UINT/const char* style, size_t mask){{</pre>}}

The argument for this message depends on the subtype.

* If `FGSETSTYLE_POINTER` is specified, the style overriden with whatever the argument is.
* If `FGSETSTYLE_NAME` is specified, the index and mask is looked up via the style hash, and an appropriate style is selected from the skin.
* If `FGSETSTYLE_INDEX` is specified, the behavior depends on the argument. If the argument is -1, the element's style is **not changed**. Instead, the style is inherited from the element's parent. Otherwise, the element's style is set to the given index and mask given in the second argument, and an appropriate style pointer is looked up in the skin.

If the style is not `NULL`, it's messages are applied to the element.

{{<pre cpp>}}FG_UINT FG_GETSTYLE(){{</pre>}}

If the element has no style (it's style is set to -1), retrieves the style from it's parent. Otherwise, returns the element's style integer.

{{<pre cpp>}}bool FG_GOTFOCUS(){{</pre>}}

Elements cannot get focus, so the default handler simply sends this message to its parent.

{{<pre cpp>}}bool FG_DRAW(const AbsRect* area, const fgDrawAuxData* aux){{</pre>}}

Calls the standard draw function, passing through the arguments.

{{<pre cpp>}}bool FG_INJECT(const FG_Msg* msg, const AbsRect* area){{</pre>}}

Calls the standard injection function, passing through the arguments.

{{<pre cpp>}}bool FG_SETNAME(const char* name){{</pre>}}

Sets the name, or deletes it if name is `NULL`.

{{<pre cpp>}}const char* FG_GETNAME(){{</pre>}}

Gets the current element name, or `NULL` if it doesn't have one.

{{<pre cpp>}}AbsVec* FG_GETDPI(){{</pre>}}

Calls `FG_GETDPI` on the parent, since an element doesn't store DPI. Normally, only [Root]({{< ref "feathergui/docs/Root.md" >}}) and [Monitor]({{< ref "feathergui/docs/Monitor.md" >}}) elements store explicit DPI values.

{{<pre cpp>}}bool FG_SETDPI(FABS x, FABS y){{</pre>}}

While this doesn't actually set anything (since an element doesn't store DPI), it propogates the message to its children, which allow controls to respond to a DPI change.

{{<pre cpp>}}float FG_GETLINEHEIGHT(){{</pre>}}

Call `FG_GETLINEHEIGHT` on the parent, since an element doesn't store lineheight. Only the [Root]({{< ref "feathergui/docs/Root.md" >}}), [Text]({{< ref "feathergui/docs/Text.md" >}}), and [Textbox]({{< ref "feathergui/docs/Textbox.md" >}})

{{<pre cpp>}}bool FG_TOUCHBEGIN(FABS x, FABS y){{</pre>}}

Translates a `FG_TOUCHBEGIN` message into an `FG_MOUSEDOWN` event (pretending the left mouse button was pressed), and passes this message back into the element for reprocessing.

{{<pre cpp>}}bool FG_TOUCHEND(FABS x, FABS y){{</pre>}}

Translates a `FG_TOUCHEND` message into an `FG_MOUSEUP` event, and passes this message back into the element for reprocessing.

{{<pre cpp>}}bool FG_TOUCHMOVE(FABS x, FABS y){{</pre>}}

Translates a `FG_TOUCHMOVE` message into an `FG_MOUSEMOVE` event, and passes this message back into the element for reprocessing.

{{<pre cpp>}}bool FG_SETDIM[FGDIM and/or FGUNIT](FABS x, FABS y){{</pre>}}

Depending on the value of the subtype, either sets the minimum or maximum dimensions of the element, applying the FGUNIT flags if they're included in the subtype.

{{<pre cpp>}}AbsVec* FG_GETDIM[FGDIM](){{</pre>}}

Depending on the value of the subtype, eitehr gets the minimum or maximum dimensions of the element. If the subtype is invalid, returns `NULL`.

{{<pre cpp>}}void* FG_GETUSERDATA(const char* key){{</pre>}}

if `key` is `NULL`, gets the raw userdata from the element. Otherwise, it looks up the key in the userhash, and if it exists, returns the corresponding value. Otherwise, returns `NULL`.

{{<pre cpp>}}bool FG_SETUSERDATA(void* value, const char* key = nullptr){{</pre>}}

If `key` is `NULL`, sets the raw userdata. Otherwise, it creates a userhash if necessary, and inserts the key/value pair.

{{<pre cpp>}}bool FG_MOUSEDBLCLICK(){{</pre>}}

Translates a double-click event into an `FG_MOUSEDOWN` message and sends it back into the element for reprocessing.

{{<pre cpp>}}bool FG_SETSCALING(FABS x, FABS y){{</pre>}}

Sets the element's scaling factor to `{x,y}`.

{{<pre cpp>}}AbsVec* FG_GETSCALING(){{</pre>}}

Gets the element's scaling factor (defaults to `{1,1}`).

{{<pre cpp>}}bool FG_CLEAR(){{</pre>}}

Removes all **foreground** elements that do not have the `INTERNAL` flag set.

{{<pre cpp>}}bool FG_NEUTRAL(){{</pre>}}

Sets the "neutral" style.

{{<pre cpp>}}bool FG_HOVER(){{</pre>}}

Sets the "hover" style.

{{<pre cpp>}}bool FG_ACTIVE(){{</pre>}}

Sets the "active" style.

{{<pre cpp>}}fgElement* FG_DEBUGMESSAGE(){{</pre>}}

Returns a pointer to this element. This is useful for debugging the injection function, or when you want to find out what element would have recieved an injected message.

## Member Functions
{{<pre cpp>}}FG_EXTERN void fgElement_InternalSetup(
  fgElement* BSS_RESTRICT self,
  fgElement* BSS_RESTRICT parent,
  fgElement* BSS_RESTRICT next,
  const char* name,
  fgFlag flags,
  const fgTransform* transform,
  fgMsgType units,
  void (*destroy)(void*),
  size_t(*message)(void*, const FG_Msg*));
{{</pre>}}

This is an internal function used by controls to perform the initial `fgElement` setup. This function is what should be called by a control's `_Init` function, **not** `fgElement_Init`, because this also sets the `destroy` and `message` functions to their appropriate values *before* calling the constructor, which is crucial for proper behavior.

{{<pre cpp>}}FG_EXTERN void fgElement_Init(
  fgElement* BSS_RESTRICT self,
  fgElement* BSS_RESTRICT parent,
  fgElement* BSS_RESTRICT next,
  const char* name,
  fgFlag flags,
  const fgTransform* transform,
  fgMsgType units);
{{</pre>}}

This performs initial setup for an `Element` control. It should only be used when initializing an actual `Element`, it should never be called inside the `_Init` function of another control or in any sort of constructor - use `fgElement_InternalSetup` for that. This sets the name, flags, and transform, then sends itself the `FG_CONSTRUCT` message, and *then* sends itself the `FG_SETPARENT` message using the `parent` and `next` arguments. This is necessary to ensure that a fully constructed object is sent to `FG_SETPARENT`, which will then set the element's skin appropriately.

{{<pre cpp>}}FG_EXTERN void fgElement_Destroy(fgElement* self);{{</pre>}}

This is the destructor for the base `fgElement` class, and should eventually be called by **all** controls. It destroys all information associated with the control, deletes the userhash, deletes the listeners, removes any focus, capture or hover states and moves those states to other controls, and performs several other debug checks.

{{<pre cpp>}}FG_EXTERN size_t fgElement_Message(fgElement* self, const FG_Msg* msg);{{</pre>}}

This is the root message processing function that all controls should eventually call to handle a message.

{{<pre cpp>}}FG_EXTERN fgElement* fgElement_GetChildUnderMouse(fgElement* self, float x, float y, AbsRect* cache);{{</pre>}}

If there is a **foreground** child that intersects the given mouse coordinates, returns a pointer to that child, otherwise returns `NULL`.

{{<pre cpp>}}FG_EXTERN void fgElement_ClearListeners(fgElement* self);{{</pre>}}

Deletes all listeners associated with this element in the listener hash. Called by `fgElement_Destroy`.

{{<pre cpp>}}FG_EXTERN size_t fgElement_CheckLastFocus(fgElement* self);{{</pre>}}

This is a helper function for parents that checks the state of `lastfocus`. If a child previously held focus before the parent lost focus, this function will restore that child's focus and return 1, otherwise it returns 0.

{{<pre cpp>}}FG_EXTERN void fgElement_ApplyMessageArray(fgElement* search, fgElement* target, fgVector* src);{{</pre>}}

This applies a message array (the `fgVector` argument is cast to `MESSAGEARRAY`) to `target` using the `search` term - only members of the message array intended for `search` will be applied to `target`. Usually, `search` is either NULL or equal to `target`.

{{<pre cpp>}}FG_EXTERN void fgElement_IterateUserHash(fgElement* self, void(*f)(void*, const char*, size_t), void* data);{{</pre>}}

This performs an iteration over the userhash values by calling `f` on all of them. The `void* p` term can be used to send a delegate or lambda, if desired.

## Helper Functions

{{<pre cpp>}}FG_EXTERN void fgElement_StyleToMessageArray(
  const struct _FG_STYLE* src,
  fgElement* target,
  fgVector** vdest);
{{</pre>}}

Given a style and a target element that style is applied to, appends the style messages to the message array (`vdest` is cast to a `MESSAGEARRAY`).

