---
description: Description of '!lbrdump' command in HyperDbg.
---

# !lbrdump (dump Last Branch Record entries)

### Command

> !lbrdump

### Syntax

> !lbrdump \[core CoreId (hex)]

### Description

Dumps the Last Branch Record (LBR) entries.

{% hint style="info" %}
Before using this command, you need to enable the LBR using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command.
{% endhint %}

### Parameters

**\[core CoreId (hex)] (optional)**

Optional value to dump the LBR entries from a specific core. Add `core xx` to your command; thus, the LBR entries from core `xx` will be dumped. If you don't specify this option, then by default, the LBR entries from the current core are dumped.

### Examples

The following command dumps LBR entries from the current core.

```c
HyperDbg> !lbrdump
```

The following command dumps LBR entries from core **1**.

```c
HyperDbg> !lbrdump core 1
```

### IOCTL

None

### Remarks

Starting from **v0.19**, this command was added to the HyperDbg debugger.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)
