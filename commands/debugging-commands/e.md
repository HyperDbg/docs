---
description: Description of 'eb, ed, eq' commands in HyperDbg.
---

# eb, ed, eq (edit virtual memory)

### Command

> eb : edit memory as Byte values
>
> ed : edit memory as Double-word values (4 bytes)
>
> eq : edit memory as Quad-word values (8 bytes)

### Syntax

> eb \[Address (hex)] \[Contents (hex)] \[pid ProcessId (hex)]
>
> ed \[Address (hex)] \[Contents (hex)] \[pid ProcessId (hex)]
>
> eq \[Address (hex)] \[Contents (hex)] \[pid ProcessId (hex)]

### Description

Edits the **virtual** address memory contents.

### Parameters

**\[Address (hex)]**

The **virtual** address of where we want to edit its memory.

**\[Contents (hex)]**

The new contents in hex format (it could be an array).

**\[pid ProcessId (hex)] (optional)**

The process ID in the hex format that we want to see the memory from its context (**cr3**).

{% hint style="info" %}
If you don't specify the `pid`, then the default `pid` is the current process (**HyperDbg**) process layout of memory.
{% endhint %}

{% hint style="danger" %}
In the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), the **pid** (parameter) is ignored. If you want to view another process memory, use the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' command to switch to another process memory layout.
{% endhint %}

### Examples

The following command is used when we want to edit the content of memory at `nt!Kd_DEFAULT_Mask` in a hex byte form and change it to `0xff 0xff 0xff 0xff`(modify **four** bytes).

```diff
HyperDbg> eb nt!Kd_DEFAULT_Mask ff ff ff ff
```

The following command is used when we want to edit the content of memory at `nt!Kd_DEFAULT_Mask+@rax+10` in a hex byte form and change it to `0xff 0xff 0xff 0xff`(modify **four** bytes).

```diff
HyperDbg> eb nt!Kd_DEFAULT_Mask+@rax+10 ff ff ff ff
```

The following command is used when we want to edit the content of memory at ``fffff800`3ad6f010`` in a hex byte form and change it to `0x90 0x90 0x90` (modify **three** bytes).

```diff
HyperDbg> eb fffff800`3ad6f010 90 90 90
```

The following example is used when we want to edit the contents of memory at ``fffff800`3ad6f010`` in Double-word values (4 bytes), change it to `245C8948` .

```diff
HyperDbg> ed fffff800`3ad6f010 245C8948
```

The following example is used when we want to edit the contents of memory at ``fffff800`3ad6f010`` in Quad-word values (8 bytes), change it to ``88889898`85858686`` and``92929393`97979898`` (16 bytes).

```diff
0: kHyperDbg> dq fffff800`3ad6f010 88889898`85858686 92929393`97979898
```

### SDK

To write the memory in the target debuggee, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_write_memory(PVOID                     destination_address,
                        DEBUGGER_EDIT_MEMORY_TYPE memory_type,
                        UINT32                    process_id,
                        PVOID                     source_address,
                        UINT32                    number_of_bytes);
```

### Remarks

* You can change as many bytes as you need in **byte**, **dword**, and **qword** formats; just add new values to the end of the command.

If you change the memory address that you previously set a breakpoint using the '[bp](https://docs.hyperdbg.org/commands/debugging-commands/bp)' command, the previous value is replaced when you remove the breakpoint.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!eb, !ed, !eq (edit physical memory)](https://docs.hyperdbg.org/commands/extension-commands/e)
