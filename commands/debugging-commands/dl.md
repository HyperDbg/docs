---
description: Description of the 'dl' command in HyperDbg.
---

# dl (traverse through linked list using virtual address)

### Command

> dl

### Syntax

> dl \[Address (hex)] \[o Offset (hex)] \[l Count (hex)] \[pid ProcessId (hex)]

### Description

Walks a linked list starting at the specified **virtual** address and shows each node.

### Parameters

**\[Address (hex)]**

The **virtual** address of the head of the linked list.

**\[o Offset (hex)] (optional)**

The offset (in bytes) within each node structure where the pointer to the next node is stored. (default: **0**)

**\[l Count (hex)] (optional)**

The maximum number of nodes to walk. (default: **100**)

**\[pid ProcessId (hex)] (optional)**

The Process ID (in the hex format) that we want to read memory from its context (**cr3**).

{% hint style="info" %}
If you don't specify the **pid**, then the default **pid** is the current process (**HyperDbg**) process layout of memory.
{% endhint %}

{% hint style="danger" %}
In the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), the **pid** (parameter) is ignored. If you want to view another process memory, use the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' command to switch to another process memory layout.
{% endhint %}

### Examples

The following command walks the `nt!PsActiveProcessHead` linked list from its virtual address.

```diff
0: kHyperDbg> dl nt!PsActiveProcessHead
walking linked list (virtual address) from fffff805c4305880 (offset = 0)

00: fffff805c4305880
01: ffffa88b8e6a4218
02: ffffa88b8e763258
03: ffffa88b9221b218
04: ffffa88b92a21258
05: ffffa88b92d20318
06: ffffa88b92d37258
07: ffffa88b92d64258
08: ffffa88b92daa258
09: ffffa88b92d16258
0a: ffffa88b92edf418
0b: ffffa88b92f0d318
0c: ffffa88b929c9258

...

(list is circular, returned to head)
```

The following command walks the linked list starting at `@rax` with an offset of `8` bytes to the next pointer.

```diff
0: kHyperDbg> dl @rax o 8
walking linked list (virtual address) from ffffa88b8e6a4000 (offset = 8)

00: ffffa88b8e6a4000
01: ffffa88b8e763000
02: ffffa88b9221b000
...

(list is circular, returned to head)
```

The following command walks the linked list at `fffff8077356f010` with an offset of `8`, a maximum of `0x20` nodes, from the memory layout of process `4`.

```diff
0: kHyperDbg> dl fffff8077356f010 o 8 l 20 pid 4
walking linked list (virtual address) from fffff8077356f010 (offset = 8)

00: fffff8077356f010
01: ffffa88b8e6a4218
02: ffffa88b8e763258
...

(stopped after 20 nodes; use 'l Count' to see more)
```

### SDK

To walk a linked list in the target debuggee, you need to use the following function in `libhyperdbg`:

```clike
VOID
hyperdbg_u_show_memory_linked_list(UINT64                    target_address,
                                   DEBUGGER_READ_MEMORY_TYPE memory_type,
                                   UINT32                    pid,
                                   UINT64                    offset,
                                   UINT64                    max_nodes);
```

### Remarks

* If you don't specify the **offset**, the default offset is `0`, meaning the next-pointer is at the very start of the node.
* If you don't specify the **count**, HyperDbg walks at most `0x100` nodes by default.
* For circular lists (e.g., Windows `LIST_ENTRY`-based lists), HyperDbg automatically detects when the walk returns to the head and stops.

{% hint style="warning" %}
Please note that you should specify a space between 'o' and the offset and between 'l' and the count in HyperDbg. For example, 'o8' is invalid, but 'o 8' is valid.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!dl (traverse through linked list using physical address)](https://docs.hyperdbg.org/commands/extension-commands/dl)

[db, dc, dd, dq (read virtual memory)](https://docs.hyperdbg.org/commands/debugging-commands/d)

[dt (display and map virtual memory to structures)](https://docs.hyperdbg.org/commands/debugging-commands/dt)

