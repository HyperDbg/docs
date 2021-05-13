---
description: Description of 'Break' function in HyperDbg Scripts
---

# Pause

### Function

> Pause

### Syntax

> Pause \( \);

### Parameters

No argument.

### Description

Breaks and halts the system and give control to the kernel debugger.

### Examples

`pause();`

Break and halt the system and give control to the debugger.

### **Remarks**

{% hint style="danger" %}
You can **only** use this function in **Debugger Mode**, and you **cannot** use it on VMI Mode or local debugging.
{% endhint %}

