---
description: Description of 'print' command in HyperDbg.
---

# print \(evaluate and print expression in debuggee\)

## Command

> print

## Syntax

> print \[expression\]

## Description

Shows the result of an expression that will be executed in the remote debuggee.

## Parameters

**\[expression\]**

```text
      The expression is based on HyperDbg's [scripting language](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations).
```

## Examples

The following command shows the `@rax` _\*\*_register in the debuggee.

```diff
0: kHyperDbg> print @rax
```

The following command shows the data as an 8-byte hex, pointed by the `@rcx` register.

```diff
0: kHyperDbg> print dq(@rcx))
```

The following command shows the value pointed by `$proc+@rdx` which **$proc** is equivalent to current `_EPROCESS` added to the **rdx** register.

```diff
0: kHyperDbg> print $proc+@rdx
```

The following command shows the value of an address, which first, **rax** register is added with **0xa0** constant then a dereference occurs and the target is shown as a QWORD hex.

```diff
0: kHyperDbg> print poi(@rax+a0)
```

## IOCTL

This commands works over serial by sending the serial packets to the remote computer.

First of all, you should fill the following structure, set the `ScriptBufferSize` and `ScriptBufferPointer` to the values you got from the script engine interpreter, and leave the `Result`and set the `IsFormat` to **false**.

After that, you should move the interpreted buffer to the end of the structure \(this structure is a header for the interpreted buffer\).

```c
typedef struct _DEBUGGEE_SCRIPT_PACKET {

  UINT32 ScriptBufferSize;
  UINT32 ScriptBufferPointer;
  BOOLEAN IsFormat;
  UINT32 Result;

  //
  // The script buffer is here
  //

} DEBUGGEE_SCRIPT_PACKET, *PDEBUGGEE_SCRIPT_PACKET;
```

Next step is sending the above structure to the debuggee when debuggee is paused and waiting for new command on **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_RUN_SCRIPT` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_RUNNING_SCRIPT
```

In the returned structure, the `Result` is filled by the kernel.

If the `Result` is `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then the operation was successful. Otherwise, the returned result is an error.

The following function is responsible for sending script buffers in the debugger.

```c
BOOLEAN KdSendScriptPacketToDebuggee(UINT64 BufferAddress, UINT32 BufferLength, UINT32 Pointer, BOOLEAN IsFormat);
```

## **Remarks**

This command is exactly like `print(expr);` in script engine, except that HyperDbg automatically adds `print(` and `);`.

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

## Requirements

None

## Related

None

