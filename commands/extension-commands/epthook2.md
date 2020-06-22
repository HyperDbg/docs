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

This command is much faster than **!epthook**, but it has the following limitations:

* It can be used only in kernel addresses, means that you cannot use it for user mode addresses.
* You can only use one hook in a page of memory, for example, if you put a hook on `fffff80126551006` then you cannot put another hook in the range of `fffff80126551000` to `fffff80126551fff`  because it's within the same page \(`0x1000` or `4096` bytes\).
* It has the limitation of classic detours hooks, we patch **18** bytes for our detours hook so when you put a hook anywhere in your assembly, you have to make sure that there is no relative jump or relative call within **18** bytes after the hook address. Most of the time, the start address of the function is detours-compatible \(doesn't start with relative jumps or relative calls\), specially in x64 fast call functions thus start address of a function is a good point to put these hidden hooks.
* `r11` is modified in the hook so you need to make sure that the target address doesn't use this register \(at least at that point of the program\). If you need the `r11` compile the HyperDbg with another register \(change the register in `EptHookWriteAbsoluteJump` and `EptHookWriteAbsoluteJump2`in `Ept.c`\).

### .Requirements

Post-Nehalem Processor \(EPT\)

Processor with Execute-only Pages Support

### Related

None

