---
description: Description of '!s*' command in HyperDbg.
---

# !sb, !sd, !sq \(search physical memory\)

### Command

> !sb : search physical memory as Byte values
>
> !sd : search physical memory as Double-word values \(4 bytes\)
>
> !sq : search physical memory as Quad-word values \(8 bytes\)

### Syntax

> !sb \[search from address \(hex\)\] l \[length \(hex\)\] \[byte pattern \(hex\)\] pid \[process id \(hex\)\]
>
> !sd \[search from address \(hex\)\] l \[length \(hex\)\] \[byte pattern \(hex\)\] pid \[process id \(hex\)\]
>
> !sq \[search from address \(hex\)\] l \[length \(hex\)\] \[byte pattern \(hex\)\] pid \[process id \(hex\)\]

### Description

Searches the **physical** memory for a special byte\(s\).

### Parameters

**\[search from address \(hex\)\]**

The **physical** address of where we want to start searching from its address.

**l \[length \(hex\)\]**

Length of the searching area.

**\[byte pattern \(hex\)\]**

Search for these bytes \(pattern\)

**pid \[process id \(hex\)\]  \(optional\)**

The process ID in the hex format that we want to see the memory from its context \(**cr3**\).

{% hint style="danger" %}
Process ID doesn't make sense in physical memory. If you specify **pid** for physical memory, ****then it is ignored.
{% endhint %}

### Examples

The following command is used to search for `4156415748`starting from `76f010` to `76f010+ffff`.

```diff
HyperDbg> !sb 76f010 l ffff 41 56 41 57 48 
```

The following example is used when we want to search for `f0cc8549`from `9016AD`to `9016AD+fff` in a different process \(process id = `1dd0`\).

```diff
HyperDbg> !sd 9016AD pid 1dd0 l fff f0cc8549 
```

The following example is used when we want to search for `f0cc8549`from `@rax+@rbx+5`to `@rax+@rbx+5+fff` in a different process \(process id = `1dd0`\).

```diff
HyperDbg> !sd @rax+@rbx+5 pid 1dd0 l fff f0cc8549 
```

The following example is used when we want to search for ``0f450000`00c0888b`` ``8b410000`0092b1b7`` from `76f010` to `76f010+100`.

```diff
HyperDbg> !sq 76f010 l 100 0f450000`00c0888b 8b410000`0092b1b7
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER_SEARCH_MEMORY`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_SEARCH_MEMORY {

  UINT64 Address;                            // Target adddress to start searching
  UINT64 Length;                             // Length of bytes to search
  UINT32 ProcessId;                          // specifies the process id
  DEBUGGER_SEARCH_MEMORY_TYPE MemoryType;    // Type of memory
  DEBUGGER_SEARCH_MEMORY_BYTE_SIZE ByteSize; // Modification size
  UINT32 CountOf64Chunks;
  UINT32 FinalStructureSize;

} DEBUGGER_SEARCH_MEMORY, *PDEBUGGER_SEARCH_MEMORY;
```

 The `Address` is where we want to start searching from its memory, and it can be both a **physical** address or a **virtual** address.

`ProcessId` is the process that we want to modify based on its memory layout \(**cr3**\), it can't be `null` or zero.

`MemoryType` shows whether the `Address` is a **physical** address or a **virtual** address.

You can see its values in the following enum :

```c
typedef enum _DEBUGGER_SEARCH_MEMORY_TYPE {
  SEARCH_PHYSICAL_MEMORY,
  SEARCH_VIRTUAL_MEMORY
} DEBUGGER_SEARCH_MEMORY_TYPE;
```

`ByteSize` shows whether we want to search the target Address in a **byte**, **dword**, or **qword** format. 

```c
typedef enum _DEBUGGER_SEARCH_MEMORY_BYTE_SIZE {
  SEARCH_BYTE,
  SEARCH_DWORD,
  SEARCH_QWORD
} DEBUGGER_SEARCH_MEMORY_BYTE_SIZE;
```

The above structure is added on top of an array of 64-bit values, which is the new content to the memory. 

For example, if you want to search in the memory address of  the target for`0x90 0x90` then you should provide an array of `0x0000000000000090` and `0x0000000000000090` and append it to the end of the above structure. The count of these chunks is stored at `CountOf64Chunks` in the above structure and the final buffer that will be sent into the kernel has a size of `FinalStructureSize` bytes. 

Also, you should provide a buffer \(size = `MaximumSearchResults * sizeof(UINT64)`\) as the output buffer, so the kernel-mode module will fill this buffer with a 64-bit array or addresses that match our search results.

You can read the result buffer as an `UINT64` array, and if you encounter a null entry, then it means there is no other result.

### **Remarks**

* You can search for as many bytes as you need in **byte**, **dword**, and **qword** formats. Just add the multiple byte\(s\) values to the end of the command. 
* In the current implementation of **physical** memory search, the address should also be available and mapped to the target process's **virtual** address space.

{% hint style="info" %}
Physical addresses are not validated in HyperDbg, which means if you access an invalid physical address, then the debuggee halts or crashes.
{% endhint %}

This command will continue the debuggee for some time \(in Debugger Mode\). This means that you lose the current context \(registers & memory\) after executing this command.

### Requirements

None

### Related

None

