---
description: Description of '!hide' command in HyperDbg.
---

# !hide \(enable transparent-mode\)

### Command

> !hide

### Syntax

> !hide \[pid \| name\] \[process id \(hex value\) \| name \(string\)\]

### Description

Enables the transparent-mode of **HyperDbg** for anti-debugging and anti-hypervisor methods. This option only works for the processes that are selected by you and won't be applied to all the processes.

You should run '**!hide**' after running '[!measure](https://docs.hyperdbg.com/commands/extension-commands/measure)' command.

{% hint style="success" %}
You can use this command multiple times. 
{% endhint %}

{% hint style="info" %}
This command is case-sensitive for the **`name`** parameter.
{% endhint %}

### Parameters

**\[pid \| name\]**

          If you want to use the process id then you should specify **pid** and if you want to enter the process name then you should specify the **name** as this argument. 

**\[process id \(hex value\) \| name \(string\)\]**

          Name or process id of the process that you want to make **HyperDbg** transparent for it.

{% hint style="info" %}
You should append `.exe` to your process names. 
{% endhint %}

### Examples

If you want to hide **HyperDbg** for process id  **`2a78`**.

```text
HyperDbg> !hide pid 2a78
```

If you want to hide **HyperDbg** for all the processes that their process names start with `procexp.exe` .

```text
HyperDbg> !hide name procexp.exe
```

### IOCTL

You should send the `IOCTL_DEBUGGER_HIDE_AND_UNHIDE_TO_TRANSPARENT_THE_DEBUGGER` IOCTL to enable or disable transparent-mode.

The following structure shows whether enable or disable it.

`IsHide = TRUE` : Enable transparent-mode.

`CpuidAverage`, `CpuidStandardDeviation`, `CpuidMedian` can be computed by using the`TransparentModeCheckHypervisorPresence` function.

`RdtscAverage`, `RdtscStandardDeviation`, `RdtscMedian` can be computed by using the `TransparentModeCheckRdtscpVmexit` function.

`TrueIfProcessIdAndFalseIfProcessName` if this field is `TRUE` then you should fill the `ProcId` with the process id that you need to transparent **HyperDbg** for that process.

Otherwise and if you want to use a process name \(not process id\) then you should set the `TrueIfProcessIdAndFalseIfProcessName` to `FALSE` and append the process name \(string\) to the bottom of this structure and put the string size + 1 \(null terminator\) to the `LengthOfProcessName`. 

Then you should send the `sizeof(DEBUGGER_HIDE_AND_TRANSPARENT_DEBUGGER_MODE)+ProcessNameStringSize` as the input size of `DeviceIoControl`.

```c
typedef struct _DEBUGGER_HIDE_AND_TRANSPARENT_DEBUGGER_MODE {

  BOOLEAN IsHide;

  UINT64 CpuidAverage;
  UINT64 CpuidStandardDeviation;
  UINT64 CpuidMedian;
  UINT64 RdtscAverage;
  UINT64 RdtscStandardDeviation;
  UINT64 RdtscMedian;
  BOOLEAN TrueIfProcessIdAndFalseIfProcessName;
  UINT32 ProcId;
  UINT32 LengthOfProcessName; // in the case of !hide name xxx, this parameter
                              // shows the length of xxx

  UINT64 KernelStatus; /* DEBUGEER_OPERATION_WAS_SUCCESSFULL ,
                          DEBUGEER_ERROR_UNABLE_TO_HIDE_OR_UNHIDE_DEBUGGER
                          */

} DEBUGGER_HIDE_AND_TRANSPARENT_DEBUGGER_MODE,
    *PDEBUGGER_HIDE_AND_TRANSPARENT_DEBUGGER_MODE;
```

If the results were successful then kernel sends the `DEBUGEER_OPERATION_WAS_SUCCESSFULL` to user-mode as `KernelStatus`  in the above structure and if it was unsuccessful then it sends`DEBUGEER_ERROR_UNABLE_TO_HIDE_OR_UNHIDE_DEBUGGER` which is an indicator of error.

You can send the above structure multiple times if you want to hide multiple processes.

### **Remarks**

This command will not guarantee to provide **100%** transparency, especially in nested-virtualization environments.

**HyperDbg** will protect you from **user-mode** anti-hypervisor methods by making vm-exits transparent even in a nested-virtualization environment; however, there are other traces for anti-VMware, anti-VirtualBox, etc. methods and these methods are still problematic, because, **HyperDbg** tries to hide itself from anti-debugging and anti-hypervisor methods not VMware, VirtualBox, etc. thus you need to run this command in a physical-machine \(not in a nested-virtualization environment\); otherwise you should find other traces for virtual machine software and solve those traces by yourself \(e.g., hooking anti-VMware APIs and Queries\).

### Requirements

None

### Related

[!measure \(measuring and providing details for transparent-mode\)](https://docs.hyperdbg.com/commands/extension-commands/measure)

[!unhide \(disable transparent-mode\)](https://docs.hyperdbg.com/commands/extension-commands/unhide)

