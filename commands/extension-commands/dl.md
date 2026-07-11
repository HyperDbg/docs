---
description: Description of the '!dl' command in HyperDbg.
---

# !dl (traverse through linked list using physical address)

### Command

> !dl

### Syntax

> !dl \[Address (hex)] \[o Offset (hex)] \[l Count (hex)]

### Description

Walks a linked list starting at the specified **physical** address and shows each node.

### Parameters

**\[Address (hex)]**

The **physical** address of the head of the linked list.

**\[o Offset (hex)] (optional)**

The offset (in bytes) within each node structure where the pointer to the next node is stored. (default: **0**)

**\[l Count (hex)] (optional)**

The maximum number of nodes to walk. (default: **100**)

{% hint style="danger" %}
Process ID doesn't make sense in physical memory. If you specify **pid**, then it is ignored.
{% endhint %}

### Examples

The following command walks the linked list starting at physical address `101305880`.

```diff
0: kHyperDbg> !dl 101305880
walking linked list (physical address) from 101305880 (offset = 0)

00: 101305880
01: 1068a4218
02: 106963258
03: 43281b218
04: 41cc21258
05: 41e920318
06: 41e937258
07: 41e964258
08: 41e9aa258
09: 41e916258
0a: 41fedf418
0b: 41ff0d318
0c: 41a5c9258

...

(list is circular, returned to head)
```

The following command walks the linked list at `@rax+@rbx` with an offset of `8` bytes to the next pointer.

Note that the result of `@rax+@rbx` is `0x101305880` in this case.

```diff
0: kHyperDbg> !dl @rax+@rbx o 8
walking linked list (physical address) from 101305880 (offset = 8)

00: 101305880
01: 1068a4210
02: 106963250
...

(list is circular, returned to head)
```

### SDK

To walk a linked list in the target debuggee using a physical address, you need to use the following function in `libhyperdbg`:

```clike
VOID
hyperdbg_u_show_memory_linked_list(UINT64                    target_address,
                                   DEBUGGER_READ_MEMORY_TYPE memory_type,
                                   UINT32                    pid,
                                   UINT64                    offset,
                                   UINT64                    max_nodes);
```

Pass `DEBUGGER_READ_PHYSICAL_ADDRESS` as `memory_type` when calling this function for physical addresses.

### Remarks

* If you don't specify the **offset**, the default offset is `0`, meaning the next-pointer is at the very start of the node.
* If you don't specify the **count**, HyperDbg walks at most `0x100` nodes by default.
* For circular lists, HyperDbg automatically detects when the walk returns to the head and stops.

{% hint style="warning" %}
Please note that you should specify a space between 'o' and the offset and between 'l' and the count in HyperDbg. For example, 'o8' is invalid, but 'o 8' is valid.
{% endhint %}

{% hint style="info" %}
Physical addresses are not validated in HyperDbg, which means if you access an invalid physical address, then the debuggee halts or crashes.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[dl (traverse through linked list using virtual address)](https://docs.hyperdbg.org/commands/debugging-commands/dl)

[!db, !dc, !dd, !dq (read physical memory)](https://docs.hyperdbg.org/commands/extension-commands/d)

[!dt (display and map physical memory to structures)](https://docs.hyperdbg.org/commands/extension-commands/dt)

