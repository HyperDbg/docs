---
description: Description of '!unhide' command in HyperDbg.
---

# !unhide \(disable transparent-mode\)

### Command

> !unhide

### Syntax

> !unhide

### Description

Disables the transparent-mode of **HyperDbg**.

### Parameters

None

### Examples

```text
HyperDbg> !unhide
```

### IOCTL

You should send the `IOCTL_DEBUGGER_HIDE_AND_UNHIDE_TO_TRANSPARENT_THE_DEBUGGER` IOCTL to enable or disable transparent-mode.

The following structure shows whether enable or disable it.

`IsHide = TRUE` : Enable transparent-mode.

`IsHide = FALSE`: Disable transparent-mode.

You should not fill anything else if you want to disable transparent-mode, just set the `IsHide`.

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

If the results were successful, then the kernel sends `DEBUGEER_OPERATION_WAS_SUCCESSFULL` to user-mode \(as KernelStatus to the above structure\), and if it was unsuccessful, then `DEBUGEER_ERROR_DEBUGGER_ALREADY_UHIDE` which is an indicator that **HyperDbg** was not already in transparent-mode.

### **Remarks**

None

### Requirements

None

### Related

[!hide \(enable transparent mode\)](https://docs.hyperdbg.com/commands/extension-commands/hide)

