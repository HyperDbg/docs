---
description: Description of 'flush' command in HyperDbg.
---

# flush \(remove pending kernel buffers and messages\)

### Command

> flush

### Syntax

> flush

### Description

This command removes all the possible pending buffers and messages from **all the commands \(not just the command that you disabled or removed\)** that are stored to be received by the user-mode from the kernel-mode and when you press '[g](https://docs.hyperdbg.com/commands/debugging-commands/g)', you no longer see any results from previous commands; however, some commands might continue generating new messages, and those new messages won't be removed. 

### Parameters

None

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER__FLUSH_LOGGING_BUFFERS`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_FLUSH_LOGGING_BUFFERS {

  UINT32 KernelStatus;
  UINT32 CountOfMessagesThatSetAsReadFromVmxRoot;
  UINT32 CountOfMessagesThatSetAsReadFromVmxNonRoot;

} DEBUGGER_FLUSH_LOGGING_BUFFERS, *PDEBUGGER_FLUSH_LOGGING_BUFFERS;
```

You don't need to fill anything from the above structure, this structure will be filled by the kernel and if the `KernelStatus` was equal to `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then you can see count of messages that are deleted from vmx-root buffer from `CountOfMessagesThatSetAsReadFromVmxRoot` and vmx non-root buffer from `CountOfMessagesThatSetAsReadFromVmxNonRoot`.

### **Remarks**

You can configure **autoflush** mode in [settings](https://docs.hyperdbg.com/commands/debugging-commands/settings), this way, **HyperDbg** removes the pending messages automatically when you **disabled** or **cleared** an event.

### Requirements

None

### Related

[settings \(configures different options and preferences\)](https://docs.hyperdbg.com/commands/debugging-commands/settings)

