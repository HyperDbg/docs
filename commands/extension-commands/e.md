---
description: Description of '!eb, !ed, !eq' commands in HyperDbg.
---

# !eb, !ed, !eq (edit physical memory)

### Command

> !eb : edit memory as Byte values
>
> !ed : edit memory as Double-word values (4 bytes)
>
> !eq : edit memory as Quad-word values (8 bytes)

### Syntax

> !eb \[Address (hex)] \[Contents (hex)]
>
> !ed \[Address (hex)] \[Contents (hex)]
>
> !eq \[Address (hex)] \[Contents (hex)]

### Description

Edits the **physical** address memory contents.

### Parameters

**\[Address (hex)]**

The **physical** address of where we want to edit its memory.

**\[Contents (hex)]**

The new contents in hex format.

{% hint style="danger" %}
Process ID doesn't make sense in physical memory. If you specify **pid** for physical memory, it is ignored.
{% endhint %}

### Examples

The following command is used when we want to edit the content of physical memory at `1000` in a hex byte form and change it to `0x90 0x90 0x90` (modify **three** bytes).

```diff
HyperDbg> !eb 1000 90 90 90
```

The following example is used when we want to edit the contents of physical memory at `1000` in Double-word values (4 bytes), change it to `245C8948` .

```diff
HyperDbg> !ed 1000 245C8948
```

The following example is used when we want to edit the contents of physical memory at `@rax+@rbx+5` in Double-word values (4 bytes), change it to `245C8948` .

```diff
HyperDbg> !ed @rax+@rbx+5 245C8948
```

The following example is used when we want to edit the contents of physical memory at `1000` in Quad-word values (8 bytes), change it to ``88889898`85858686`` and``92929393`97979898`` (16 bytes).

```diff
0: kHyperDbg> !dq 1000 88889898`85858686 92929393`97979898
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

* You can change as many bytes as you need in **byte**, **dword**, and **qword** formats. Just add new values to the end of the command.

If you change the memory address that you previously set a breakpoint using the '[bp](https://docs.hyperdbg.org/commands/debugging-commands/bp)' command, the previous value is replaced when you remove the breakpoint.

{% hint style="info" %}
Physical addresses are not validated in HyperDbg, which means if you access an invalid physical address, then the debuggee halts or crashes.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[eb, ed, eq (edit virtual memory)](https://docs.hyperdbg.org/commands/debugging-commands/e)
