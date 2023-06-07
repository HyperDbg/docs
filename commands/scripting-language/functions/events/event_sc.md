---
description: Description of 'event_enable' function in HyperDbg Scripts
---

# event\_sc

### Function

> event\_sc

### Syntax

> event\_sc( EnableOrDisable );

### Parameters

**\[ Expression (EnableOrDisable)]**

Whether the short-circuiting should be enabled or disabled for the corresponding execution of the event.

### Description

Enables or disables the '[event short-circuiting](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)', only for the **corresponding** execution of the event.

### Examples

`event_sc(1);`

Enables short-circuiting for the event; thus, the **corresponding execution** of the event will be **ignored**.

`event_sc(0);`

Disables short-circuiting for the event; thus, the **corresponding execution** of the event **WON'T** be **ignored**.

### Remarks

{% hint style="info" %}
Please keep in mind, **disabling** means that the event will be **executed** while **enabling** means the event emulation will be **ignored** or **short-circuited**.
{% endhint %}

Starting from **v0.3**, this feature was added to the HyperDbg debugger.

### Related

[Event short-circuiting](https://docs.hyperdbg.org/tips-and-tricks/misc/event-short-circuiting)
