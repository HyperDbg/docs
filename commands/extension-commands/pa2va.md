---
description: Description of '!pa2va' command in HyperDbg.
---

# !pa2va \(convert physical address to virtual address\)

### Command

> !pa2va

### Syntax

> !pa2va \[Physical Address\] pid \[Process Id \(hex value\)\]

### Description

Converts the PHYSICAL address to the VIRTUAL address.

### Parameters

**\[Physical Address\]**

          The target **physical** address

**\[Process Id \(hex value\)\]**

          The **process id** of where you want to convert the address based on it \(if you don't specify this parameter, then the system process memory layout is used\)

### Examples

The following command shows the physical address of `21c9370`.

```diff
HyperDbg> !va2pa 21c9370
FFFFF8004EBC9370
```

The following command shows the physical address of the result of evaluating `@rax+@rbx+5`.

```diff
HyperDbg> !va2pa @rax+@rbx+5
FFFFF8004EB65546
```

The following command shows the physical address of `21c9370`in the process layout of process id \(0x4\).

```diff
HyperDbg> !va2pa 21c9370 pid 0x4
FFFFF8004EBC9370
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER_VA2PA_AND_PA2VA_COMMANDS`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_VA2PA_AND_PA2VA_COMMANDS {

  UINT64 VirtualAddress;
  UINT64 PhysicalAddress;
  UINT32 ProcessId;
  BOOLEAN IsVirtual2Physical;

} DEBUGGER_VA2PA_AND_PA2VA_COMMANDS, *PDEBUGGER_VA2PA_AND_PA2VA_COMMANDS;
```

You should only fill the **VirtualAddress** of the above structure when you want a physical address and fill the above **PhysicalAddress** when you want a virtual address. Also, set **IsVirtual2Physical** to **`true`** in the case of virtual-to-physical and set it to **`false`** in the case of physical-to-virtual.

If you want to convert based on another process memory layout, then put its process ID. Otherwise, put the current process id on it. **ProcessId** can't be `null`.

### **Remarks**

{% hint style="success" %}
If the physical address or process id does not exist, then it shows **`0`**.
{% endhint %}

This command will continue the debuggee for some time \(in Debugger Mode\). This means that you lose the current context \(registers & memory\) after executing this command.

### Requirements

None

### Related

[!va2pa \(convert a virtual address to physical address\)](https://docs.hyperdbg.com/commands/extension-commands/va2pa)

