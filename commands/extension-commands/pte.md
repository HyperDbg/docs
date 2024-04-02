---
description: Description of the '!pte' command in HyperDbg.
---

# !pte (display page-level address and entries)

### Command

> !pte

### Syntax

> !pte \[VirtualAddress (hex)] \[pid ProcessId (hex)]

### Description

Displays the **PML4E**, **PDPTE**, **PDE**, **PTE** for the specified address.

### Parameters

**\[VirtualAddress (hex)]**

The **virtual** address of where we want to read its page-level entries.

**\[pid ProcessId (hex)] (optional)**

The Process Id of where you want to convert the address based on it.

{% hint style="info" %}
If you don't specify the **pid**, then the default **pid** is the current process (**HyperDbg**) process layout of memory.
{% endhint %}

{% hint style="danger" %}
In the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), the **pid** (parameter) is ignored. If you want to view another process memory, use the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' command to switch to another process memory layout.
{% endhint %}

### Examples

The following command shows the page-level entries `nt!ExAllocatePoolWithTag`.

```diff
HyperDbg> !pte nt!ExAllocatePoolWithTag
VA fffff801639b1030
PML4E (PXE) at ffffeaf57abd5f80 contains 0000000005109063
PDPTE (PPE) at ffffeaf57abf0028 contains 000000000510a063
PDE at ffffeaf57e0058e0 contains 0a000000034000a1
PDE is a large page, so it doesn't have a PTE
```

The following command shows the page-level entries `nt!ExAllocatePoolWithTag+5`.

```diff
HyperDbg> !pte nt!ExAllocatePoolWithTag+5
VA fffff801639b1035
PML4E (PXE) at ffffeaf57abd5f80 contains 0000000005109063
PDPTE (PPE) at ffffeaf57abf0028 contains 000000000510a063
PDE at ffffeaf57e0058e0 contains 0a000000034000a1
PDE is a large page, so it doesn't have a PTE
```

The following command shows the page-level entries `fffff80040f00c28`.

```diff
HyperDbg> !pte fffff80040f00c28
VA fffff80040f00c28
PML4E (PXE) at ffff83c1e0f07f80 contains 0000000004108063
PDPTE (PPE) at ffff83c1e0ff0008 contains 000000000411c063
PDE at ffff83c1fe001038 contains 0000000004124063
PTE at ffff83fc00207800 contains 8900000006470863
```

The following command shows the page-level entries `fffff8003ad6f010`. Note that some entries might have a large **PDE** and no **PTE**.

```diff
HyperDbg> !pte fffff8003ad6f010
VA fffff8003ad6f010
PML4E (PXE) at ffff83c1e0f07f80 contains 0000000004108063
PDPTE (PPE) at ffff83c1e0ff0000 contains 0000000004109063
PDE at ffff83c1fe000eb0 contains 00000000028008e3
PDE is a large page, so it doesn't have a PTE
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER_READ_PAGE_TABLE_ENTRIES_DETAILS`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_READ_PAGE_TABLE_ENTRIES_DETAILS
{
    UINT64 VirtualAddress;
    UINT32 ProcessId;

    UINT64 Pml4eVirtualAddress;
    UINT64 Pml4eValue;

    UINT64 PdpteVirtualAddress;
    UINT64 PdpteValue;

    UINT64 PdeVirtualAddress;
    UINT64 PdeValue;

    UINT64 PteVirtualAddress;
    UINT64 PteValue;

    UINT32 KernelStatus;

} DEBUGGER_READ_PAGE_TABLE_ENTRIES_DETAILS,
    *PDEBUGGER_READ_PAGE_TABLE_ENTRIES_DETAILS;
```

You should only fill the **VirtualAddress** of the above structure when the IOCTL returns from the kernel, other parts of this structure are filled with valid entry virtual addresses and the entry value itself.

If you want to convert based on another process memory layout, then put its process ID. Otherwise, put the current process id on it. **ProcessId** is ignored in debugger mode.

You can map the value to each entry's structure (Look at Intel SDM for more information).

Also, the structures are available in `MemoryMapper.h` but they might be outdated.

### Remarks

{% hint style="success" %}
If the **VirtualAddress** and Value of entry for PDE and PTE from the kernel IOCTL are the same, the entry has a LARGE PDE and doesn't have PTE.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

None
