---
description: Description of 'bd' command in HyperDbg.
---

# bd \(disable breakpoints\)

### Command

> bd

### Syntax

> bd \[breakpoint id \(hex value\)\]

### Description

Disables a previously enabled breakpoint \(**0xcc**\).

### Parameters

**\[breakpoint id \(hex value\)\]**

          The **breakpoint id** of the target breakpoint. You can see a list of breakpoints and breakpoint ids using the '[bl](https://docs.hyperdbg.org/commands/debugging-commands/bl)' command.

### Examples

Imagine we have the following active breakpoints.

```text
0: kHyperDbg> bl
id   address           status
--   ---------------   --------
01   fffff801639b1030  enabled
02   fffff801639b1035  enabled
03   fffff801639b103a  enabled
04   fffff801639b103f  enabled
```

After executing the following command, it's now disabled.

```text
0: kHyperDbg> bd 2
```

If you see the list of active breakpoints again, you can see that it's disabled.

```text
0: kHyperDbg> bl
id   address           status
--   ---------------   --------
01   fffff801639b1030  enabled
02   fffff801639b1035  disabled
03   fffff801639b103a  enabled
04   fffff801639b103f  enabled
```

### IOCTL

This commands works over serial by sending the serial packets to the remote computer.

First of all, you should fill the following structure, set the `BreakpointId` to your special breakpoint id, which is derived from the '[bl](https://docs.hyperdbg.org/commands/debugging-commands/bl)' command.

```c
typedef struct _DEBUGGEE_BP_LIST_OR_MODIFY_PACKET {

  UINT64 BreakpointId;
  DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST Request;
  UINT32 Result;

} DEBUGGEE_BP_LIST_OR_MODIFY_PACKET, *PDEBUGGEE_BP_LIST_OR_MODIFY_PACKET;
```

In the request field, choose one of the actions from the following enum.

```c
typedef enum _DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST {

  DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST_LIST_BREAKPOINTS,
  DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST_ENABLE,
  DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST_DISABLE,
  DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST_CLEAR,

} DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST;
```

In the case of `Request`:

* If you want to list all the active breakpoint, then choose `DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST_LIST_BREAKPOINTS`.
* If you want to enable a breakpoint, then choose `DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST_ENABLE`.
* If you want to disable a breakpoint, then choose `DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST_DISABLE`.
* If you want to list clear and remove a breakpoint, then choose `DEBUGGEE_BREAKPOINT_MODIFICATION_REQUEST_CLEAR`.

Note that if you want to list breakpoints, there is no need to fill `BreakpointId`and HyperDbg will ignore it.

The next step is sending the above structure to the debuggee when debuggee is paused and waiting for new command on **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_LIST_OR_MODIFY_BREAKPOINTS` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_LIST_OR_MODIFY_BREAKPOINTS
```

In the returned structure, the `Result` is filled by the kernel.

If the `Result` is `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then the operation was successful. Otherwise, the returned result is an error.

The following function is responsible for sending list/modify breakpoint buffers in the debugger.

```c
BOOLEAN KdSendListOrModifyPacketToDebuggee(PDEBUGGEE_BP_LIST_OR_MODIFY_PACKET ListOrModifyPacket);
```

### **Remarks**

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

[bp \(set breakpoint\)](https://docs.hyperdbg.org/commands/debugging-commands/bp)

[bl \(list breakpoints\)](https://docs.hyperdbg.org/commands/debugging-commands/bl)

[be \(enable breakpoints\)](https://docs.hyperdbg.org/commands/debugging-commands/be)

[bc \(clear and remove breakpoints\)](https://docs.hyperdbg.org/commands/debugging-commands/bc)

