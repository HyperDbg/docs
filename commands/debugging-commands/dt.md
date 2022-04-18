---
description: Description of the 'dt' command in HyperDbg.
---

# dt (display and map virtual memory to structures)

### Command

> dt

### Syntax

> dt \[Module!SymbolName (string)] \[AddressExpression (string)] \[pid ProcessId (hex)] \[padding Padding (yesno)] \[offset Offset (yesno)] \[bitfield Bitfield (yesno)] \[native Native (yesno)] \[decl Declaration (yesno)] \[def Definitions (yesno)] \[func Functions (yesno)] \[pragma Pragma (yesno)] \[prefix Prefix (string)] \[suffix Suffix (string)] \[inline Expantion (string)] \[output FileName (string)]

### Description

Displays data structures in an offset format, maps virtual address to a structure and shows the different fields and their values.

{% hint style="success" %}
If you want to make **C** (header) code structures, enums, and data types from the symbols, you can use the '[struct](https://docs.hyperdbg.org/commands/debugging-commands/struct)' command.
{% endhint %}

### Parameters

**\[Module!SymbolName (string)]**

Test.

**\[AddressExpression (string)] (optional)**

**\[pid ProcessId (hex)] (optional)**

The process ID in the hex format that we want to see the memory from its context (**cr3**).

**\[padding Padding (yesno)] (optional)**



**\[offset Offset (yesno)] (optional)**



**\[bitfield Bitfield (yesno)] (optional)**



**\[native Native (yesno)] (optional)**



**\[decl Declaration (yesno)] (optional)**



**\[def Definitions (yesno)] (optional)**



**\[func Functions (yesno)] (optional)**



**\[pragma Pragma (yesno)] (optional)**



**\[prefix Prefix (string)] (optional)**



**\[suffix Suffix (string)] (optional)**



**\[inline Expantion (string)] (optional)**



**\[output FileName (string)] (optional)**



{% hint style="info" %}
If you don't specify the pid, then the default pid is the current process (**HyperDbg**) process layout of memory.
{% endhint %}

### IOCTL

The **IOCTL** for this command is implemented like [db, dc, dd, dq (read virtual memory)](https://docs.hyperdbg.org/commands/debugging-commands/d) command to read data from memory.

### Remarks

For implementing this command, [**pdbex**](https://github.com/wbenny/pdbex) is integrated into HyperDbg.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!dt (display and map physical memory to structures)](https://docs.hyperdbg.org/commands/extension-commands/dt)

[struct (make structures, enums, data types from symbols)](https://docs.hyperdbg.org/commands/debugging-commands/struct)

[Mapping Data & Create Structures, and Enums From Symbols](https://docs.hyperdbg.org/using-hyperdbg/kernel-mode-debugging/examples/basics/mapping-data-and-create-structures-and-enums-from-symbols)
