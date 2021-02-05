---
description: Description of '?' command in HyperDbg.
---

# ? \(evaluate and execute expressions and scripts in debuggee\)

### Command

> ?

### Syntax

> ? \[expression\]

### Description

Evaluates and executes an expression in the remote debuggee.

### Parameters

**\[expression\]**

          The expression is based on HyperDbg's [scripting language](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations).

### Examples

The following command shows the `@rax` ****register in the debuggee by calling the [print](https://docs.hyperdbg.com/commands/scripting-language/functions/print) function.

```diff
HyperDbg> ? print(@rax);
```

### IOCTL

This commands works over serial by sending the serial packets to the remote computer.

First of all, you should fill the following structure, set the `ScriptBufferSize` and `ScriptBufferPointer` to the values you got from the script engine interpreter, and leave the `Result`.

After that, you should move the interpreted buffer to the end of the structure \(this structure is a header for the interpreted buffer\).

```c
typedef struct _DEBUGGEE_SCRIPT_PACKET {

  UINT32 ScriptBufferSize;
  UINT32 ScriptBufferPointer;
  UINT32 Result;

  //
  // The script buffer is here
  //

} DEBUGGEE_SCRIPT_PACKET, *PDEBUGGEE_SCRIPT_PACKET;
```

The next step is sending the above structure to the debuggee when debuggee is paused and waiting for new command on **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_RUN_SCRIPT` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_RUNNING_SCRIPT
```

In the returned structure, the `Result` is filled by the kernel.

If the `Result` is `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then the operation was successful. Otherwise, the returned result is an error.

The following function is responsible for sending script buffers in the debugger.

```c
BOOLEAN KdSendScriptPacketToDebuggee(UINT64 BufferAddress, UINT32 BufferLength, UINT32 Pointer);
```

### **Remarks**

None

### Requirements

None

### Related

None

