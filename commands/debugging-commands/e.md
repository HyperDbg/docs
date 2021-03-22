---
description: Description of 'e*' command in HyperDbg.
---

# eb, ed, eq \(edit virtual memory\)

### Command

> eb : edit memory as Byte values
>
> ed : edit memory as Double-word values \(4 bytes\)
>
> eq : edit memory as Quad-word values \(8 bytes\)

### Syntax

> eb \[address\] \[new value \(hex\)\] pid \[process id \(hex\)\]
>
> ed \[address\] \[new value \(hex\)\] pid \[process id \(hex\)\]
>
> eq \[address\] \[new value \(hex\)\] pid \[process id \(hex\)\]

### Description

Edits the **virtual** address memory contents.

### Parameters

**\[Address\]**

          The **virtual** address of where we want to edit its memory.

**\[new value \(hex\)\]**

          The new contents in hex format

**pid \[process id\]  \(optional\)**

          The process ID in the hex format that we want to see the memory from its context \(**cr3**\).

{% hint style="info" %}
If you don't specify the pid, then the default pid is the current process \(**HyperDbg**\) process layout of memory.
{% endhint %}

### Examples

The following command is used when we want to edit the content of memory at ``fffff800`3ad6f010`` in a hex byte form and change it to `0x90 0x90 0x90` \(modify **three** bytes\).

```diff
HyperDbg> eb fffff800`3ad6f010 90 90 90
```

The following example is used when we want to edit the contents of memory at ``fffff800`3ad6f010`` in Double-word values \(4 bytes\), change it to `245C8948` .

```diff
HyperDbg> ed fffff800`3ad6f010 245C8948
```

The following example is used when we want to edit the contents of memory at ``fffff800`3ad6f010`` in Quad-word values \(8 bytes\), change it to ``88889898`85858686``  and``92929393`97979898`` \(16 bytes\).

```diff
HyperDbg> dq fffff800`3ad6f010 88889898`85858686 92929393`97979898
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER_EDIT_MEMORY`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_EDIT_MEMORY {

  UINT32 Result;                           // Result from kernel
  UINT64 Address;                          // Target adddress to modify
  UINT32 ProcessId;                        // specifies the process id
  DEBUGGER_EDIT_MEMORY_TYPE MemoryType;    // Type of memory
  DEBUGGER_EDIT_MEMORY_BYTE_SIZE ByteSize; // Modification size
  UINT32 CountOf64Chunks;
  UINT32 FinalStructureSize;

} DEBUGGER_EDIT_MEMORY, *PDEBUGGER_EDIT_MEMORY;
```

The `Result` will be filled by the kernel-mode driver when it returns from the kernel and shows whether the editing was successful or not. The following results can come from the kernel:

```c
#define DEBUGGER_ERROR_EDIT_MEMORY_STATUS_INVALID_PARAMETER 0xc000000b
#define DEBUGGER_ERROR_EDIT_MEMORY_STATUS_INVALID_ADDRESS_BASED_ON_CURRENT_PROCESS \
  0xc000000c
#define DEBUGGER_ERROR_EDIT_MEMORY_STATUS_INVALID_ADDRESS_BASED_ON_OTHER_PROCESS \
  0xc000000d
```

 The `Address` is where we want to modify, and it can be both a **physical** address or a **virtual** address.

`ProcessId` is the process that we want to modify based on its memory layout \(**cr3**\), it can't be `null` or zero.

`MemoryType` shows whether the `Address` is a **physical** address or a **virtual** address.

You can see its values in the following enum :

```c
typedef enum _DEBUGGER_EDIT_MEMORY_TYPE {
  EDIT_PHYSICAL_MEMORY,
  EDIT_VIRTUAL_MEMORY
} DEBUGGER_EDIT_MEMORY_TYPE;
```

`ByteSize` shows whether we want to modify the target Address in a **byte**, **dword**, or **qword** format. 

```c
typedef enum _DEBUGGER_EDIT_MEMORY_BYTE_SIZE {
  EDIT_BYTE,
  EDIT_DWORD,
  EDIT_QWORD
} DEBUGGER_EDIT_MEMORY_BYTE_SIZE;
```

The above structure is added on top of an array of 64-bit values, which is the new content to the memory. 

For example, if you want to change the memory address of  the target to `0x90 0x90` then you should provide an array of `0x0000000000000090` and `0x0000000000000090` and append it to the end of the above structure. The count of these chunks is stored at `CountOf64Chunks` in the above structure and the final buffer that will be sent into the kernel has a size of `FinalStructureSize` bytes. 

### **Remarks**

* You can change as many bytes as you need in **byte**, **dword**, and **qword** formats; just add new values to the end of the command. 

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

[!eb, !ed, !eq \(edit physical memory\)](https://docs.hyperdbg.com/commands/extension-commands/e)

