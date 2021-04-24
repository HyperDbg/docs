---
description: Description of 'd*' command in HyperDbg.
---

# db, dc, dd, dq \(read virtual memory\)

### Command

> db : read memory as Byte values and ASCII characters
>
> dc : read memory as Double-word values \(4 bytes\) and ASCII characters
>
> dd : read memory as Double-word values \(4 bytes\)
>
> dq : read memory as Quad-word values \(8 bytes\)

### Syntax

> db \[address\] l \[length \(hex\)\] pid \[process id \(hex\)\]
>
> dc \[address\] l \[length \(hex\)\] pid \[process id \(hex\)\]
>
> dd \[address\] l \[length \(hex\)\] pid \[process id \(hex\)\]
>
> dq \[address\] l \[length \(hex\)\] pid \[process id \(hex\)\]

### Description

Shows the **virtual** address memory content in hex form.

### Parameters

**\[Address\]**

          The **virtual** address of where we want to read its memory.

**l \[Length\] \(optional\)**

          The length \(byte\) in hex format

**pid \[process id\]  \(optional\)**

          The process ID in hex format that we want to see the memory from its context \(**cr3**\).

{% hint style="info" %}
If you don't specify the **pid**, then the default **pid** is the current process \(HyperDbg\) process layout of memory.
{% endhint %}

{% hint style="danger" %}
In the [Debugger Mode](https://docs.hyperdbg.com/using-hyperdbg/prerequisites/operation-modes#debugger-mode), the **pid** \(parameter\) is ignored. If you want to view another process memory, use the '[.process](https://docs.hyperdbg.com/commands/meta-commands/.process)' command to switch to another process memory layout.
{% endhint %}

### Examples

The following command is used when we want to read the content of memory at **``fffff800`3ad6f010``** with length of `0x50` from the memory layout view of process \(`4` a.k.a. system process\) in a hex byte format.

```diff
HyperDbg> db fffff800`3ad6f010 l 50 pid 4
fffff800`3ad6f010  48 89 5C 24 08 48 89 6C 24 10 48 89 74 24 18 57  H.\$.H.l$.H.t$.W
fffff800`3ad6f020  41 56 41 57 48 83 EC 30 65 48 8B 04 25 20 00 00  AVAWH..0eH.. ..
fffff800`3ad6f030  00 33 DB 44 0F B7 3D C5 3F 20 00 41 8B E8 48 8B  .3.D..=.? .A..H.
fffff800`3ad6f040  F2 89 5C 24 68 8B F9 4C 8B 88 C0 00 00 00 45 0F  ..\$h..L......E.
fffff800`3ad6f050  B7 B1 92 00 00 00 41 8B C6 44 8B C8 89 5C 24 20  ......A..D...\$
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Double-word value \(4 bytes\) and ASCII characters format.

```diff
HyperDbg> dc fffff800`3ad6f010
fffff800`3ad6f010  245C8948 6C894808 89481024 57182474  H.\$.H.l$.H.t$.W
fffff800`3ad6f020  57415641 30EC8348 048B4865 00002025  AVAWH..0eH.. ..
fffff800`3ad6f030  44DB3300 C53DB70F 4100203F 8B48E88B  .3.D..=.? .A..H.
fffff800`3ad6f040  245C89F2 4CF98B68 00C0888B 0F450000  ..\$h..L......E.
fffff800`3ad6f050  0092B1B7 8B410000 C88B44C6 20245C89  ......A..D...\$
fffff800`3ad6f060  48C58B44 CF8BD68B CC3693E8 C08548FF  D..H......6..H..
fffff800`3ad6f070  0038840F 8B480000 6C8B48D8 8B485824  ..8...H..H.l$XH.
fffff800`3ad6f080  5C8B48C3 8B485024 48602474 4130C483  .H.\$PH.t$`H..0A
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Double-word values \(4 bytes\) format with the length of `0x10`.

```diff
HyperDbg> dd fffff800`3ad6f010 l 10
fffff800`3ad6f010  245C8948 6C894808 89481024 57182474
```

The following example shows the content of memory at ``fffff800`3ad6f010`` from current process layout in a Quad-word values \(8 bytes\) format.

```diff
0: kHyperDbg> dq fffff800`3ad6f010
fffff800`3ad6f010  6C894808`245C8948 57182474`89481024
fffff800`3ad6f020  30EC8348`57415641 00002025`048B4865
fffff800`3ad6f030  C53DB70F`44DB3300 8B48E88B`4100203F
fffff800`3ad6f040  4CF98B68`245C89F2 0F450000`00C0888B
fffff800`3ad6f050  8B410000`0092B1B7 20245C89`C88B44C6
fffff800`3ad6f060  CF8BD68B`48C58B44 C08548FF`CC3693E8
fffff800`3ad6f070  8B480000`0038840F 8B485824`6C8B48D8
fffff800`3ad6f080  8B485024`5C8B48C3 4130C483`48602474
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

Where `Pid` is the process id, `Address` is the target location address and `size` is the length of the byte\(s\) that you need to read. 

`MemoryType`is either **virtual** or **physical**.

```c
typedef enum _DEBUGGER_READ_MEMORY_TYPE { DEBUGGER_READ_PHYSICAL_ADDRESS, DEBUGGER_READ_VIRTUAL_ADDRESS } DEBUGGER_READ_MEMORY_TYPE;
```

`ReadingType` is either from the **kernel** or from the **vmx-root**. Currently, only the reading from the kernel is implemented.

```c
typedef enum _DEBUGGER_READ_READING_TYPE { READ_FROM_KERNEL, READ_FROM_VMX_ROOT } DEBUGGER_READ_READING_TYPE;
```

If you don't want to read from the kernel directly, use the following **HyperDbg Routine**.

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

{% hint style="warning" %}
Please note that you should specify space between 'l' and the length in HyperDbg. For example, 'l10' is invalid, but 'l 10' is valid. \(It's opposed to windbg\).
{% endhint %}

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

[!db, !dc, !dd, !dq \(read physical memory\)](https://docs.hyperdbg.com/commands/extension-commands/d)

