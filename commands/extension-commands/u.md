---
description: Description of '!u' command in HyperDbg.
---

# !u \(disassemble physical address\)

### Command

> !u

### Syntax

> !u \[address\] l \[length \(hex\)\]

### Description

Shows the assembly regarding to memory content at the **physical** address hex form.

### Parameters

\[Address\]

          The **physical** address of where we want to start to disassemble its memory

l \[Length\] \(optional\)

          The length \(byte\) in hex format

{% hint style="danger" %}
Process ID doesn't make sense in physical memory. If you specify **pid** then it ignored.
{% endhint %}

### Examples

The following command is used when we want to disassemble the content of memory at **`1000`** with length of `0x50`bytes.

```diff
HyperDbg >!u 1000 l 50
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

The following example shows the assembly content of memory at `1000`.

```diff
HyperDbg >!u 1000
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

Where `Pid` is the process id, `Address` is the target location address and `size` is the length of byte that you need to read. 

`MemoryType`is either **virtual** or **physical**.

```c
typedef enum _DEBUGGER_READ_MEMORY_TYPE { DEBUGGER_READ_PHYSICAL_ADDRESS, DEBUGGER_READ_VIRTUAL_ADDRESS } DEBUGGER_READ_MEMORY_TYPE;
```

`ReadingType` is either from **kernel** or form **vmx root**, currently only the reading from kernel is implemented.

```c
typedef enum _DEBUGGER_READ_READING_TYPE { READ_FROM_KERNEL, READ_FROM_VMX_ROOT } DEBUGGER_READ_READING_TYPE;
```

If you don't want to directly read from kernel, use the following **HyperDbg Routine**.

```c
void HyperDbgReadMemoryAndDisassemble(DEBUGGER_SHOW_MEMORY_STYLE Style, UINT64 Address,
                        DEBUGGER_READ_MEMORY_TYPE MemoryType,
                        DEBUGGER_READ_READING_TYPE ReadingType, UINT32 Pid,
                        UINT Size);
```

The above function fill the IOCTL structure and shows the memory content, it also is able to disassemble the memory, you can specify one of the following `styles` to show the memory.

```c
typedef enum _DEBUGGER_SHOW_MEMORY_STYLE { DEBUGGER_SHOW_COMMAND_DISASSEMBLE, DEBUGGER_SHOW_COMMAND_DB, DEBUGGER_SHOW_COMMAND_DC, DEBUGGER_SHOW_COMMAND_DQ, DEBUGGER_SHOW_COMMAND_DD } DEBUGGER_SHOW_MEMORY_STYLE;
```

**For disassembling use the `DEBUGGER_SHOW_COMMAND_DISASSEMBLE` as the `Style`.**

### **Remarks**

* If you don't specify the length, the default length for HyperDbg is 0x40 Bytes.

{% hint style="warning" %}
Please note that you should specify space between 'l' and the length, for HyperDbg for example 'l10' is invalid but 'l 10' is valid. \(It's opposes to windbg\).
{% endhint %}

{% hint style="success" %}
HyperDbg uses [Zydis](https://zydis.re/) as its core disassembler.
{% endhint %}

### Requirements

None

### Related

[Zydis](https://zydis.re/)

[u \(disassemble virtual address\)](https://docs.hyperdbg.com/commands/debugging-commands/u)

