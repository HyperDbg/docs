---
description: Description of '!d*' command in HyperDbg.
---

# !db, !dc, !dd, !dq \(read physical memory\)

### Command

> !db : read memory as Byte values and ASCII characters
>
> !dc : read memory as Double-word values \(4 bytes\) and ASCII characters
>
> !dd : read memory as Double-word values \(4 bytes\)
>
> !dq : read memory as Quad-word values \(8 bytes\)

### Syntax

> !db \[address\] l \[length \(hex\)\]
>
> !dc \[address\] l \[length \(hex\)\]
>
> !dd \[address\] l \[length \(hex\)\]
>
> !dq \[address\] l \[length \(hex\)\]

### Description

Shows the **physical** address memory content in hex form.

### Parameters

**\[Address\]**

          The **physical** address of where we want to read its memory.

**l \[Length\] \(optional\)**

          The length \(byte\) in hex format

{% hint style="danger" %}
Process ID doesn't make sense in physical memory. If you specify **pid** for physical memory, ****then it is ignored.
{% endhint %}

### Examples

The following command is used when we want to read the content of memory at **`1000`** with length of `0x50` in a hex byte format.

```diff
HyperDbg> !db 1000 l 50
#       00000000`00001000  0B 00 00 00 00 00 00 00 00 00 86 80 D3 10 02 00  ................
#       00000000`00001010  00 06 01 01 00 00 00 00 03 01 00 00 00 00 00 00  ................
#       00000000`00001020  00 10 FA 39 00 F8 FF FF 00 00 02 00 00 00 00 00  ...9............
#       00000000`00001030  03 01 00 00 00 00 00 00 00 10 FC 39 00 F8 FF FF  ...........9....
#       00000000`00001040  00 00 02 00 00 00 00 00 01 01 00 00 00 00 00 00  ................
```

The following command is used when we want to read the content of memory after evaluating the expression **`@rax+@rbx`** with length of `0x50` in a hex byte format.

Note that the result of **`@rax+@rbx`**is `0x1000` in this case.

```diff
HyperDbg> !db @rax+@rbx l 50
#       00000000`00001000  0B 00 00 00 00 00 00 00 00 00 86 80 D3 10 02 00  ................
#       00000000`00001010  00 06 01 01 00 00 00 00 03 01 00 00 00 00 00 00  ................
#       00000000`00001020  00 10 FA 39 00 F8 FF FF 00 00 02 00 00 00 00 00  ...9............
#       00000000`00001030  03 01 00 00 00 00 00 00 00 10 FC 39 00 F8 FF FF  ...........9....
#       00000000`00001040  00 00 02 00 00 00 00 00 01 01 00 00 00 00 00 0
```

The following example shows the content of memory at `1000` in a double-word value \(4 bytes\) and ASCII characters format.

```diff
HyperDbg> !dc 1000
#       00000000`00001000  0000000B 00000000 80860000 000210D3  ................
#       00000000`00001010  01010600 00000000 00000103 00000000  ................
#       00000000`00001020  39FA1000 FFFFF800 00020000 00000000  ...9............
#       00000000`00001030  00000103 00000000 39FC1000 FFFFF800  ...........9....
#       00000000`00001040  00020000 00000000 00000101 00000000  ................
#       00000000`00001050  00005000 00000000 00000020 00000000  .P...... .......
#       00000000`00001060  00000103 00000000 39F8B000 FFFFF800  ...........9....
#       00000000`00001070  00004000 00000000 00000000 00000000  .@..............
```

The following example shows the content of memory at `1000` in a double-word value \(4 bytes\) format with the length of `0x10`.

```diff
0: kHyperDbg> !dd 1000 l 10
#       00000000`00001000  0000000B 00000000 80860000 000210D3
```

The following example shows the content of memory at `1000` in a quad-word value \(8 bytes\) format.

```diff
0: kHyperDbg> !dq 1000
#       00000000`00001000  00000000`0000000B 000210D3`80860000
#       00000000`00001010  00000000`01010600 00000000`00000103
#       00000000`00001020  FFFFF800`39FA1000 00000000`00020000
#       00000000`00001030  00000000`00000103 FFFFF800`39FC1000
#       00000000`00001040  00000000`00020000 00000000`00000101
#       00000000`00001050  00000000`00005000 00000000`00000020
#       00000000`00001060  00000000`00000103 FFFFF800`39F8B000
#       00000000`00001070  00000000`00004000 00000000`00000000
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

`ReadingType` is either from the **kernel** or from the **vmx root**. Currently, only the reading from the kernel is implemented.

```c
typedef enum _DEBUGGER_READ_READING_TYPE { READ_FROM_KERNEL, READ_FROM_VMX_ROOT } DEBUGGER_READ_READING_TYPE;
```

If you don't want to read from the kernel directly, use the following **HyperDbg** Routine.

```c
void HyperDbgReadMemoryAndDisassemble(DEBUGGER_SHOW_MEMORY_STYLE Style, UINT64 Address,
                        DEBUGGER_READ_MEMORY_TYPE MemoryType,
                        DEBUGGER_READ_READING_TYPE ReadingType, UINT32 Pid,
                        UINT Size);
```

The above function fills the IOCTL structure and shows the memory content. It is also able to disassemble the memory. You can specify one of the following `styles` to show the memory.

```c
typedef enum _DEBUGGER_SHOW_MEMORY_STYLE { DEBUGGER_SHOW_COMMAND_DISASSEMBLE, DEBUGGER_SHOW_COMMAND_DB, DEBUGGER_SHOW_COMMAND_DC, DEBUGGER_SHOW_COMMAND_DQ, DEBUGGER_SHOW_COMMAND_DD } DEBUGGER_SHOW_MEMORY_STYLE;
```

For disassembling, use the `DEBUGGER_SHOW_COMMAND_DISASSEMBLE` as the `Style`.

In the debugger-mode, HyperDbg uses the exact same structure, you should send the above structure over serial to the debuggee which is paused in **vmx-root** mode.  

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_READ_MEMORY` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_READING_MEMORY
```

The following function is responsible for sending reading memory in the debugger.

```c
BOOLEAN KdSendReadMemoryPacketToDebuggee(PDEBUGGER_READ_MEMORY ReadMem);
```

### **Remarks**

* If you don't specify the length, the default length for HyperDbg is 0x80 Bytes.

HyperDbg won't remove breakpoints previously set using the '[bp](https://docs.hyperdbg.org/commands/debugging-commands/bp)' command if you're disassembling or reading the memory of a special **physical** address. However, for the virtual addresses, HyperDbg ignores breakpoints and shows the target location's real value. 

{% hint style="warning" %}
Please note that you should specify space between 'l' and the length in HyperDbg. For example, 'l10' is invalid, but 'l 10' is valid. \(It's opposed to windbg\).
{% endhint %}

{% hint style="info" %}
Physical addresses are not validated in HyperDbg, which means if you access an invalid physical address, then the debuggee halts or crashes.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

[db, dc, dd, dq \(read virtual memory\)](https://docs.hyperdbg.org/commands/debugging-commands/d)

