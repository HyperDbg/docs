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
You can use the '[**struct**](https://docs.hyperdbg.org/commands/debugging-commands/struct)' command to make **C** (header) code structures, enums, and data types from the symbols.
{% endhint %}

### Parameters

**\[Module!SymbolName (string)]**

Module name combined with the symbol name (separated by a `!` sign).

**\[AddressExpression (string)] (optional)**

Address or an expression that evaluates as a virtual address. If you leave this argument empty, the symbol data is shown without mapping to data.

**\[pid ProcessId (hex)] (optional)**

The Process ID (in the hex format) that we want to see the memory from its context (**cr3**).

**\[padding Padding (yesno)] (optional)**

Create padding members.

**\[offset Offset (yesno)] (optional)**

Show offsets.

**\[bitfield Bitfield (yesno)] (optional)**

Allow bitfields in the union.

**\[native Native (yesno)] (optional)**

Use types from stdint.h instead of native types.

**\[decl Declaration (yesno)] (optional)**

Print declarations.

**\[def Definitions (yesno)] (optional)**

Print definitions.

**\[func Functions (yesno)] (optional)**

Print functions.

**\[pragma Pragma (yesno)] (optional)**

Print #pragma pack directives.

**\[prefix Prefix (string)] (optional)**

Prefix for all symbols.

**\[suffix Suffix (string)] (optional)**

Suffix for all symbols.

**\[inline Expantion (string)] (optional)**

Specifies expansion of nested structures/unions.

&#x20;  **none**: only the top-most type is printed.

&#x20;  **unnamed**: unnamed types are nested.

&#x20;  **all**: all types are nested.

**\[output FileName (string)] (optional)**

Specifies the output file.

{% hint style="info" %}
If you don't specify the pid, then the default pid is the current process (**HyperDbg**) process layout of memory.
{% endhint %}

### IOCTL

The **IOCTL** for this command is implemented like [db, dc, dd, dq (read virtual memory)](https://docs.hyperdbg.org/commands/debugging-commands/d) command to read data from memory.

### Remarks

{% hint style="info" %}
For implementing this command, [**pdbex**](https://github.com/wbenny/pdbex) is integrated into HyperDbg.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!dt (display and map physical memory to structures)](https://docs.hyperdbg.org/commands/extension-commands/dt)

[struct (make structures, enums, data types from symbols)](https://docs.hyperdbg.org/commands/debugging-commands/struct)

[Mapping Data & Create Structures, and Enums From Symbols](https://docs.hyperdbg.org/using-hyperdbg/kernel-mode-debugging/examples/basics/mapping-data-and-create-structures-and-enums-from-symbols)
