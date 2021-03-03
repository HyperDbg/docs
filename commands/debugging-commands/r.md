---
description: Description of 'r' command in HyperDbg.
---

# r \(read or modify registers\)

### Command

> r

### Syntax

> r \[register\] \[= expr\]

### Description

Reads or modifies registers when the debuggee is paused.

### Parameters

\[register\]

          The register that needs to be read or modified

\[= expr\]

          The value or the expression that needs to be evaluated and modify the target register

{% hint style="info" %}
If you don't specify any parameters to the 'r' command, it shows all general-purpose + segment registers.
{% endhint %}

### Examples

_Todo_

### IOCTL

_Todo_

### **Remarks**

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

None

