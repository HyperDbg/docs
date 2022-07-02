---
description: Description of '!u, !u2' commands in HyperDbg.
---

# !u, !u2 (disassemble physical address)

### Command

> !u
>
> !u2

### Syntax

> !u \[Address (hex)] \[l Length (hex)]
>
> !u2 \[Address (hex)] \[l Length (hex)]

{% hint style="info" %}
**!u** disassembles as x64 and **!u2** disassembles as x86.
{% endhint %}

### Description

Shows the assembly regarding memory content at the **physical** address hex form.

### Parameters

**\[Address (hex)]**

The **physical** address of where we want to start to disassemble its memory.

**\[l Length (hex)] (optional)**

The length (byte) in hex format.

{% hint style="danger" %}
Process ID doesn't make sense in physical memory. If you specify **pid,** then it is ignored.
{% endhint %}

### Examples

The following command is used when we want to disassemble the content of memory (x64) at `1000` with length of `0x50`bytes.

```diff
HyperDbg> !u 1000 l 50
00000000`00001000 0B 00                               or eax, dword ptr ds:[rax]
00000000`00001002 00 00                               add byte ptr ds:[rax], al
00000000`00001004 00 00                               add byte ptr ds:[rax], al
00000000`00001006 00 00                               add byte ptr ds:[rax], al
00000000`00001008 00 00                               add byte ptr ds:[rax], al
00000000`0000100a 86 80 D3 10 02 00                   xchg byte ptr ds:[rax+0x210D3], al
00000000`00001010 00 06                               add byte ptr ds:[rsi], al
00000000`00001012 01 01                               add dword ptr ds:[rcx], eax
00000000`00001014 00 00                               add byte ptr ds:[rax], al
00000000`00001016 00 00                               add byte ptr ds:[rax], al
00000000`00001018 03 01                               add eax, dword ptr ds:[rcx]
00000000`0000101a 00 00                               add byte ptr ds:[rax], al
00000000`0000101c 00 00                               add byte ptr ds:[rax], al
00000000`0000101e 00 00                               add byte ptr ds:[rax], al
00000000`00001020 00 10                               add byte ptr ds:[rax], dl
00000000`00001022 FA                                  cli
00000000`00001023 39 00                               cmp dword ptr ds:[rax], eax
00000000`00001025 F8                                  clc
```

The following command is used when we want to disassemble the content of memory (x64) at `@rax+@rbx` with length of `0x50`bytes.

Note that the result of `@rax+@rbx` is `0x1000` in this case.

```diff
HyperDbg> !u @rax+@rbx l 50
00000000`00001000 0B 00                               or eax, dword ptr ds:[rax]
00000000`00001002 00 00                               add byte ptr ds:[rax], al
00000000`00001004 00 00                               add byte ptr ds:[rax], al
00000000`00001006 00 00                               add byte ptr ds:[rax], al
00000000`00001008 00 00                               add byte ptr ds:[rax], al
00000000`0000100a 86 80 D3 10 02 00                   xchg byte ptr ds:[rax+0x210D3], al
00000000`00001010 00 06                               add byte ptr ds:[rsi], al
00000000`00001012 01 01                               add dword ptr ds:[rcx], eax
00000000`00001014 00 00                               add byte ptr ds:[rax], al
00000000`00001016 00 00                               add byte ptr ds:[rax], al
00000000`00001018 03 01                               add eax, dword ptr ds:[rcx]
00000000`0000101a 00 00                               add byte ptr ds:[rax], al
00000000`0000101c 00 00                               add byte ptr ds:[rax], al
00000000`0000101e 00 00                               add byte ptr ds:[rax], al
00000000`00001020 00 10                               add byte ptr ds:[rax], dl
00000000`00001022 FA                                  cli
00000000`00001023 39 00                               cmp dword ptr ds:[rax], eax
00000000`00001025 F8                                  clc
```

The following example shows the assembly content of memory (x64) at `1000`.

```diff
HyperDbg> !u 1000
00000000`00001000 0B 00                               or eax, dword ptr ds:[rax]
00000000`00001002 00 00                               add byte ptr ds:[rax], al
00000000`00001004 00 00                               add byte ptr ds:[rax], al
00000000`00001006 00 00                               add byte ptr ds:[rax], al
00000000`00001008 00 00                               add byte ptr ds:[rax], al
00000000`0000100a 86 80 D3 10 02 00                   xchg byte ptr ds:[rax+0x210D3], al
00000000`00001010 00 06                               add byte ptr ds:[rsi], al
00000000`00001012 01 01                               add dword ptr ds:[rcx], eax
00000000`00001014 00 00                               add byte ptr ds:[rax], al
00000000`00001016 00 00                               add byte ptr ds:[rax], al
00000000`00001018 03 01                               add eax, dword ptr ds:[rcx]
00000000`0000101a 00 00                               add byte ptr ds:[rax], al
00000000`0000101c 00 00                               add byte ptr ds:[rax], al
00000000`0000101e 00 00                               add byte ptr ds:[rax], al
00000000`00001020 00 10                               add byte ptr ds:[rax], dl
00000000`00001022 FA                                  cli
00000000`00001023 39 00                               cmp dword ptr ds:[rax], eax
00000000`00001025 F8                                  clc
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER_READ_MEMORY` , you have to send it in the following structure.

```c
typedef struct _DEBUGGER_READ_MEMORY {

    UINT32 Pid; // Read from cr3 of what process
    UINT64 Address;
    UINT32 Size;
    DEBUGGER_READ_MEMORY_TYPE MemoryType;
    DEBUGGER_READ_READING_TYPE ReadingType;

} DEBUGGER_READ_MEMORY, * PDEBUGGER_READ_MEMORY;
```

Where `Pid` is the process id, `Address` is the target location address and `size` is the length of the byte that you need to read.

`MemoryType`is either **virtual** or **physical**.

```c
typedef enum _DEBUGGER_READ_MEMORY_TYPE { DEBUGGER_READ_PHYSICAL_ADDRESS, DEBUGGER_READ_VIRTUAL_ADDRESS } DEBUGGER_READ_MEMORY_TYPE;
```

`ReadingType` is either from the **kernel** or from the **vmx-root**. Currently, only the reading from the kernel is implemented.

```c
typedef enum _DEBUGGER_READ_READING_TYPE { READ_FROM_KERNEL, READ_FROM_VMX_ROOT } DEBUGGER_READ_READING_TYPE;
```

If you don't want to read from the kernel directly, use the following **HyperDbg** routine.

```c
void HyperDbgReadMemoryAndDisassemble(DEBUGGER_SHOW_MEMORY_STYLE Style, UINT64 Address,
                        DEBUGGER_READ_MEMORY_TYPE MemoryType,
                        DEBUGGER_READ_READING_TYPE ReadingType, UINT32 Pid,
                        UINT Size);
```

The above function fills the IOCTL structure and shows the memory content. It is also able to disassemble the memory. You can specify one of the following `styles` to show the memory.

```c
typedef enum _DEBUGGER_SHOW_MEMORY_STYLE { DEBUGGER_SHOW_COMMAND_DISASSEMBLE64, DEBUGGER_SHOW_COMMAND_DISASSEMBLE32, DEBUGGER_SHOW_COMMAND_DB, DEBUGGER_SHOW_COMMAND_DC, DEBUGGER_SHOW_COMMAND_DQ, DEBUGGER_SHOW_COMMAND_DD } DEBUGGER_SHOW_MEMORY_STYLE;
```

For disassembling, use the `DEBUGGER_SHOW_COMMAND_DISASSEMBLE64` as the `Style` for x64 disassembling, and for disassembling x86, use the `DEBUGGER_SHOW_COMMAND_DISASSEMBLE32`.

In the debugger mode, HyperDbg uses the exact same structure, you should send the above structure over serial to the debuggee which is paused in **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_READ_MEMORY` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_READING_MEMORY
```

The following function is responsible for sending reading memory in the debugger.

```c
BOOLEAN KdSendReadMemoryPacketToDebuggee(PDEBUGGER_READ_MEMORY ReadMem);
```

### Remarks

* If you don't specify the length, the default length for HyperDbg is 0x40 Bytes.

HyperDbg won't remove breakpoints previously set using the '[bp](https://docs.hyperdbg.org/commands/debugging-commands/bp)' command if you're disassembling or reading the memory of a special **physical** address. However, for the virtual addresses, HyperDbg ignores breakpoints and shows the target location's real value.

{% hint style="warning" %}
Please note that you should specify space between 'l' and the length in HyperDbg. For example, 'l10' is invalid, but 'l 10' is valid. (It's opposed to windbg).
{% endhint %}

{% hint style="success" %}
HyperDbg uses [Zydis](https://zydis.re/) as its core disassembler.
{% endhint %}

{% hint style="info" %}
Physical addresses are not validated in HyperDbg, which means if you access an invalid physical address, then the debuggee halts or crashes.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[Zydis](https://zydis.re/)

[u (disassemble virtual address)](https://docs.hyperdbg.org/commands/debugging-commands/u)
