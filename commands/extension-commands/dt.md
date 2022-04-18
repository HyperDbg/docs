---
description: Description of the '!dt' command in HyperDbg.
---

# !dt (display and map physical memory to structures)

### Command

> !dt

### Syntax

> !dt \[Module!SymbolName (string)] \[AddressExpression (string)] \[padding Padding (yesno)] \[offset Offset (yesno)] \[bitfield Bitfield (yesno)] \[native Native (yesno)] \[decl Declaration (yesno)] \[def Definitions (yesno)] \[func Functions (yesno)] \[pragma Pragma (yesno)] \[prefix Prefix (string)] \[suffix Suffix (string)] \[inline Expantion (string)] \[output FileName (string)]

### Description

Displays data structures in an offset format, maps a physical address to a structure and shows the different fields and their values.

{% hint style="success" %}
You can use the '[**struct**](https://docs.hyperdbg.org/commands/debugging-commands/struct)' command to make **C** (header) code structures, enums, and data types from the symbols.
{% endhint %}

### Parameters

**\[Module!SymbolName (string)]**

Module name combined with the symbol name (separated by a `!` sign).

**\[AddressExpression (string)] (optional)**

Address or an expression that evaluates as a physical address. If you leave this argument empty, the symbol data is shown without mapping to data.

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

### Examples

The following command is used to map the physical address at `ac09080` to the `nt!_EPROCESS` structure.

```clike
1: kHyperDbg> !dt nt!_EPROCESS ac09080
 _EPROCESS
  +0x0000 _KPROCESS Pcb : _KPROCESS
  +0x0438 _EX_PUSH_LOCK ProcessLock : (null)
  +0x0440 void* UniqueProcessId : 00000000`00000898
  +0x0448 _LIST_ENTRY ActiveProcessLinks : _LIST_ENTRY [ ffff948c`bf8ea4c8 - ffff948c`c002a788 ]
  +0x0458 _EX_RUNDOWN_REF RundownProtect : (null)
  +0x0460 uint32_t Flags2 : 0x200d094
  +0x0460 uint32_t JobNotReallyActive, Pos 0, 1 Bit : 0y0
  +0x0460 uint32_t AccountingFolded, Pos 1, 1 Bit : 0y0
  +0x0460 uint32_t NewProcessReported, Pos 2, 1 Bit : 0y1
  +0x0460 uint32_t ExitProcessReported, Pos 3, 1 Bit : 0y0
  +0x0460 uint32_t ReportCommitChanges, Pos 4, 1 Bit : 0y1
  +0x0460 uint32_t LastReportMemory, Pos 5, 1 Bit : 0y0
  +0x0460 uint32_t ForceWakeCharge, Pos 6, 1 Bit : 0y0
  +0x0460 uint32_t CrossSessionCreate, Pos 7, 1 Bit : 0y1
  +0x0460 uint32_t NeedsHandleRundown, Pos 8, 1 Bit : 0y0
  +0x0460 uint32_t RefTraceEnabled, Pos 9, 1 Bit : 0y0
  +0x0460 uint32_t PicoCreated, Pos 10, 1 Bit : 0y0
  +0x0460 uint32_t EmptyJobEvaluated, Pos 11, 1 Bit : 0y0
  +0x0460 uint32_t DefaultPagePriority, Pos 12, 3 Bit : 0y101
  +0x0460 uint32_t PrimaryTokenFrozen, Pos 15, 1 Bit : 0y1
  +0x0460 uint32_t ProcessVerifierTarget, Pos 16, 1 Bit : 0y0
  +0x0460 uint32_t RestrictSetThreadContext, Pos 17, 1 Bit : 0y0
  +0x0460 uint32_t AffinityPermanent, Pos 18, 1 Bit : 0y0
  +0x0460 uint32_t AffinityUpdateEnable, Pos 19, 1 Bit : 0y0
  +0x0460 uint32_t PropagateNode, Pos 20, 1 Bit : 0y0
  +0x0460 uint32_t ExplicitAffinity, Pos 21, 1 Bit : 0y0
  +0x0460 uint32_t ProcessExecutionState, Pos 22, 2 Bit : 0y00
  +0x0460 uint32_t EnableReadVmLogging, Pos 24, 1 Bit : 0y0
  +0x0460 uint32_t EnableWriteVmLogging, Pos 25, 1 Bit : 0y1
  +0x0460 uint32_t FatalAccessTerminationRequested, Pos 26, 1 Bit : 0y0
  +0x0460 uint32_t DisableSystemAllowedCpuSet, Pos 27, 1 Bit : 0y0
  +0x0460 uint32_t ProcessStateChangeRequest, Pos 28, 2 Bit : 0y00
  ...
```

### IOCTL

The **IOCTL** for this command is implemented like [!db, !dc, !dd, !dq (read physical memory)](https://docs.hyperdbg.org/commands/extension-commands/d) commands to read data from memory.

### Remarks

{% hint style="info" %}
For implementing this command, [**pdbex**](https://github.com/wbenny/pdbex) is integrated into HyperDbg.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[dt (display and map virtual memory to structures)](https://docs.hyperdbg.org/commands/debugging-commands/dt)

[struct (make structures, enums, data types from symbols)](https://docs.hyperdbg.org/commands/debugging-commands/struct)

[Mapping Data & Create Structures, and Enums From Symbols](https://docs.hyperdbg.org/using-hyperdbg/kernel-mode-debugging/examples/basics/mapping-data-and-create-structures-and-enums-from-symbols)
