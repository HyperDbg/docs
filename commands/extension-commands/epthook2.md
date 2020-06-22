---
description: Description of '!epthook2' command in HyperDbg.
---

# !epthook2 \(hidden hook with EPT - Detours\)

### Command

> !epthook2

### Syntax

> !epthook2 \[address\] \[event options\]

### Description

In-line, detours-style kernel EPT hidden hook. \(fast\)

{% hint style="info" %}
This implementation of hidden hook won't cause vm-exit when it triggers, it's like detours and everything is done in vmx non-root, so it's much faster than !epthook but it some limitations, see **Remarks** for more information.
{% endhint %}

### Parameters

\[address\]

          The **Virtual** address of where we want to start to disassemble its memory

\[event options\]

          Regular event parameters used in HyperDbg events.

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

Post-Nehalem Processor \(EPT\)

Processor with Execute-only Pages Support

### Related

