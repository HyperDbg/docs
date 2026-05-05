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

Optional value to dump the LBR entries from a specific core. Add `core xx` to your command; thus, the LBR entries from core `xx` will be dumped. If you don't specify this option, then by default, the LBR entries from all cores are dumped.

### Examples

The following command dumps LBR entries from all cores. Note that in this case, only the second core has the target entries; in other words, only the second core executed the '[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr_save)' function as a result of triggering an event.

```c
HyperDbg> !lbrdump
LBR Chronological Trace on core (decimal): 0

         no LBR entries found for this core
         you can use the 'lbr_save();' function in the script engine
         on the target core to save the LBR entries before dumping

         ===========================================================

LBR Chronological Trace on core (decimal): 1

         no LBR entries found for this core
         you can use the 'lbr_save();' function in the script engine
         on the target core to save the LBR entries before dumping

         ===========================================================

LBR Chronological Trace on core (decimal): 2

         [29] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [30] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [31] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [ 0] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [ 1] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [ 2] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [ 3] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [ 4] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [ 5] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [ 6] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [ 7] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [ 8] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [ 9] Branch Mispredicted: false, Cycle Count (Decimal): 008 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [10] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [11] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [12] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [13] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [14] Branch Mispredicted: false, Cycle Count (Decimal): 008 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [15] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [16] Branch Mispredicted: false, Cycle Count (Decimal): 013 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [17] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [18] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [19] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [20] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [21] Branch Mispredicted: false, Cycle Count (Decimal): 004 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [22] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [23] Branch Mispredicted: false, Cycle Count (Decimal): 004 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [24] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [25] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [26] Branch Mispredicted: true , Cycle Count (Decimal): 001 - From: 00007ff76c3a8d54  To: 00007ff76c3a8eef
         [27] Branch Mispredicted: false, Cycle Count (Decimal): 007 - From: 00007ff76c3a8efb  To: 00007ff76c3a913e
         [28] Branch Mispredicted: false, Cycle Count (Decimal): 015 - From: 00007ff76c3a9144  To: 00007ff76c3a2fd7
LBR Chronological Trace on core (decimal): 3

         no LBR entries found for this core
         you can use the 'lbr_save();' function in the script engine
         on the target core to save the LBR entries before dumping

         ===========================================================

LBR Chronological Trace on core (decimal): 4

         no LBR entries found for this core
         you can use the 'lbr_save();' function in the script engine
         on the target core to save the LBR entries before dumping

         ===========================================================
...
```

The following command dumps LBR entries from core **0x2**.

```c
HyperDbg> !lbrdump core 2
LBR Chronological Trace on core (decimal): 2

         [29] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [30] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [31] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [ 0] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [ 1] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [ 2] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [ 3] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [ 4] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [ 5] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [ 6] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [ 7] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [ 8] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [ 9] Branch Mispredicted: false, Cycle Count (Decimal): 008 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [10] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [11] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [12] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [13] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [14] Branch Mispredicted: false, Cycle Count (Decimal): 008 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [15] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [16] Branch Mispredicted: false, Cycle Count (Decimal): 013 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [17] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [18] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [19] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [20] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [21] Branch Mispredicted: false, Cycle Count (Decimal): 004 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [22] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [23] Branch Mispredicted: false, Cycle Count (Decimal): 004 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [24] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [25] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [26] Branch Mispredicted: true , Cycle Count (Decimal): 001 - From: 00007ff76c3a8d54  To: 00007ff76c3a8eef
         [27] Branch Mispredicted: false, Cycle Count (Decimal): 007 - From: 00007ff76c3a8efb  To: 00007ff76c3a913e
         [28] Branch Mispredicted: false, Cycle Count (Decimal): 015 - From: 00007ff76c3a9144  To: 00007ff76c3a2fd7
```

### IOCTL

None

### Remarks

Starting from **v0.19**, this command was added to the HyperDbg debugger.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr_save)

[lbr\_print](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr_print)

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)
