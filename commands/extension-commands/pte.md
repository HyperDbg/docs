---
description: Description of '!pte' command in HyperDbg.
---

# !pte \(display page-level address and entries\)

### Command

> !pte

### Syntax

> !pte \[Virtual Address\]

### Description

The **!pte** command displays the **PML4E**, **PDPTE**, **PDE**, **PTE** for the specified address.  


### Parameters

\[Virtual Address\]

          The **virtual** address of where we want to read its page-level entries

### Examples

The following command shows the page-level entries `fffff80040f00c28` .

```diff
HyperDbg >!pte fffff80040f00c28
VA fffff80040f00c28
PML4E (PXE) at ffff83c1e0f07f80 contains 0000000004108063
PDPT (PPE) at ffff83c1e0ff0008  contains 000000000411c063
PDE at ffff83c1fe001038 contains 0000000004124063
PTE at ffff83fc00207800 contains 8900000006470863
```

The following command shows the page-level entries `fffff8003ad6f010`. Note that some entries might have a large **PDE** and no **PTE**.

```diff
HyperDbg >!pte fffff8003ad6f010
VA fffff8003ad6f010
PML4E (PXE) at ffff83c1e0f07f80 contains 0000000004108063
PDPT (PPE) at ffff83c1e0ff0000  contains 0000000004109063
PDE at ffff83c1fe000eb0 contains 00000000028008e3
PDE is a large page, so it doesn't have a PTE
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER_READ_PAGE_TABLE_ENTRIES_DETAILS`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_READ_PAGE_TABLE_ENTRIES_DETAILS {

  UINT64 VirtualAddress;

  UINT64 Pml4eVirtualAddress;
  UINT64 Pml4eValue;

  UINT64 PdpteVirtualAddress;
  UINT64 PdpteValue;

  UINT64 PdeVirtualAddress;
  UINT64 PdeValue;

  UINT64 PteVirtualAddress;
  UINT64 PteValue;

} DEBUGGER_READ_PAGE_TABLE_ENTRIES_DETAILS,
    *PDEBUGGER_READ_PAGE_TABLE_ENTRIES_DETAILS;
```

You should only fill the VirtualAddress of the above structure, when the IOCTL returns from the kernel, other parts of this structure are filled with valid entry virtual addresses and the entry value itself.

You can map the value to the specific structure of each entry, look at Intel SDM for more information.

Also the structures are available in `MemoryMapper.h`, but they might be outdated.

### **Remarks**

{% hint style="success" %}
If the VirtualAddress and Value of entry for PDE and PTE from the kernel IOCTL is the same, this shows that the entry has a LARGE PDE and doesn't have PTE.
{% endhint %}

### Requirements

None

### Related

None

