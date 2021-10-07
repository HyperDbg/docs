---
description: Description of 'flush' command in HyperDbg.
---

# flush \(remove pending kernel buffers and messages\)

## Command

> flush

## Syntax

> flush

## Description

This command removes all the possible pending buffers and messages from **all the commands \(not just the command that you disabled or removed\)** that are stored to be received by the user-mode from the kernel-mode, and when you press '[g](https://docs.hyperdbg.org/commands/debugging-commands/g)', you no longer see any results from previous commands; however, some commands might continue generating new messages, and those new messages won't be removed.

## Parameters

None

## IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER__FLUSH_LOGGING_BUFFERS`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_FLUSH_LOGGING_BUFFERS {

  UINT32 KernelStatus;
  UINT32 CountOfMessagesThatSetAsReadFromVmxRoot;
  UINT32 CountOfMessagesThatSetAsReadFromVmxNonRoot;

} DEBUGGER_FLUSH_LOGGING_BUFFERS, *PDEBUGGER_FLUSH_LOGGING_BUFFERS;
```

You don't need to fill anything from the above structure. The kernel will fill the structure, and if the `KernelStatus` was equal to `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then you can see the count of messages that are deleted from the vmx-root buffer from `CountOfMessagesThatSetAsReadFromVmxRoot` and vmx non-root buffer from `CountOfMessagesThatSetAsReadFromVmxNonRoot`.

In the debugger-mode, HyperDbg uses the exact same structure, you should send the above structure over serial to the debuggee which is paused in **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_MODE_FLUSH_BUFFERS` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_FLUSH
```

In the returned structure, the `KernelStatus` is filled by the kernel.

If the `KernelStatus` is `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then the operation was successful. Otherwise, the returned result is an error.

The following function is responsible for sending flushing buffers in the debugger.

```c
BOOLEAN KdSendFlushPacketToDebuggee();
```

## **Remarks**

You can configure **autoflush** mode in [settings](https://docs.hyperdbg.org/commands/debugging-commands/settings). This way, **HyperDbg** removes the pending messages automatically when you **disabled** or **cleared** an event.

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

## Requirements

None

## Related

[settings \(configures different options and preferences\)](https://docs.hyperdbg.org/commands/debugging-commands/settings)

