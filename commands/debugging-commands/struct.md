---
description: Description of the 'struct' command in HyperDbg.
---

# struct (make structures, enums, data types from symbols)

### Command

> struct

### Syntax

> struct \[Module!SymbolName (string)] \[padding Padding (yesno)] \[offset Offset (yesno)] \[bitfield Bitfield (yesno)] \[native Native (yesno)] \[decl Declaration (yesno)] \[def Definitions (yesno)] \[func Functions (yesno)] \[pragma Pragma (yesno)] \[prefix Prefix (string)] \[suffix Suffix (string)] \[inline Expantion (string)] \[output FileName (string)]

### Description

Displays structures, enums, and data types in a **C** (header) format.

### Parameters

**\[Module!SymbolName (string)]**

Module name combined with the symbol name (separated by a `!` sign).

**\[padding Padding (yesno)] (optional)**

Create padding members. (default: **yes**)

**\[offset Offset (yesno)] (optional)**

Show offsets. (default: **yes**)

**\[bitfield Bitfield (yesno)] (optional)**

Allow bitfields in the union. (default: **no**)

**\[native Native (yesno)] (optional)**

Use types from stdint.h instead of native types. (default: **no**)

**\[decl Declaration (yesno)] (optional)**

Print declarations. (default: **yes**)

**\[def Definitions (yesno)] (optional)**

Print definitions. (default: **yes**)

**\[func Functions (yesno)] (optional)**

Print functions. (default: **no**)

**\[pragma Pragma (yesno)] (optional)**

Print #pragma pack directives. (default: **yes**)

**\[prefix Prefix (string)] (optional)**

Prefix for all symbols.

**\[suffix Suffix (string)] (optional)**

Suffix for all symbols.

**\[inline Expantion (string)] (optional)**

Specifies expansion of nested structures/unions. (default: **unnamed**)

&#x20;  **none**: only the top-most type is printed.

&#x20;  **unnamed**: unnamed types are nested.

&#x20;  **all**: all types are nested.

**\[output FileName (string)] (optional)**

Specifies the output file if the user wants to save the printed data.

### IOCTL

None

### Remarks

{% hint style="info" %}
For implementing this command, [**pdbex**](https://github.com/wbenny/pdbex) is integrated into HyperDbg.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[dt (display and map virtual memory to structures)](https://docs.hyperdbg.org/commands/debugging-commands/dt)

[!dt (display and map physical memory to structures)](https://docs.hyperdbg.org/commands/extension-commands/dt)

[Mapping Data & Create Structures, and Enums From Symbols](https://docs.hyperdbg.org/using-hyperdbg/kernel-mode-debugging/examples/basics/mapping-data-and-create-structures-and-enums-from-symbols)
