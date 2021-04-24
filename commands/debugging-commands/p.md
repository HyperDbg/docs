---
description: Description of 'p' command in HyperDbg.
---

# p \(step-over\)

### Command

> p

> pr

### Syntax

> p\[r\] \[count \(hex value\)\]

### Description

Executes a single instruction \(step-over\) and optionally displays the resulting values of all registers and flags.

### Parameters

**\[count\] \(optional\)**

          Count of step\(s\), or how many times perform the stepping operation. If you don't set this argument, then by default, the **count** is `1`.

### Examples

If you want to step-over one instruction.

```text
0: kHyperDbg> p
fffff801`68d91267    41 5B                               pop r11
```

If you want to step-over one instruction and view the registers.

```c
0: kHyperDbg> pr
fffff801`68d91269    41 5A                               pop r10
RAX=0000000000000000 RBX=ffff948cbf6599d0 RCX=0000000000000024
RDX=0000000000000000 RSI=0000000000000000 RDI=ffff948cc266d670
RIP=fffff80168d91269 RSP=ffff9305492df6a8 RBP=0000000000000002
R8=0000000000000000  R9=0000000000000000  R10=0000000048564653
R11=0000000000000000 R12=0000000000000000 R13=0000000000000000
R14=ffff948cc266d670 R15=ffff948cc058e6b0 IOPL=00
OF 0  DF 0  IF 0  SF  0
ZF 1  PF 1  CF 0  AXF 0
CS 0010 SS 0018 DS 002b ES 002b FS 0053 GS 002b
RFLAGS=0000000000040046
```

If you want to step-over for `5` instructions.

```c
0: kHyperDbg> p 5
fffff801`68d9126b    9D                                  popfq
fffff801`68d9126c    C3                                  ret
fffff801`63a12948    6A D1                               push 0xFFFFFFFFFFFFFFD1
fffff801`63a1294a    E9 B1 00 00 00                      jmp 0xFFFFF80163A12A00
fffff801`63a12b00    F6 44 24 10 01                      test byte ptr ss:[rsp+0x10], 0x01
```

### IOCTL

This commands works over serial by sending the serial packets to the remote computer.

First of all, you should fill the following structure, set the `StepType` to the type of step that you want to perform \(e.g., _step-in_, _step-over_, and _instrument step-in_\), and if it's a step-over \(only step-over\), then if the currently executing instruction is a **call** instruction, set the `IsCurrentInstructionACall` to `TRUE` and also set the length of the current call instruction \(if it's a call instruction\) to `CallLength`, so you can hint the debuggee to find the next instruction. In step-over and instrument step-over, just set the `StepType` and set all the other members to **null**.

```c
typedef struct _DEBUGGEE_STEP_PACKET {

  DEBUGGER_REMOTE_STEPPING_REQUEST StepType;

  //
  // Only in the case of call instructions
  // the 'p' command
  //
  BOOLEAN IsCurrentInstructionACall;
  UINT32 CallLength;

} DEBUGGEE_STEP_PACKET, *PDEBUGGEE_STEP_PACKET;
```

`StepType` can be chosen from one of the following types.

```c
typedef enum _DEBUGGER_REMOTE_STEPPING_REQUEST {

  DEBUGGER_REMOTE_STEPPING_REQUEST_STEP_OVER,
  DEBUGGER_REMOTE_STEPPING_REQUEST_STEP_IN,
  DEBUGGER_REMOTE_STEPPING_REQUEST_STEP_IN_INSTRUMENT,

} DEBUGGER_REMOTE_STEPPING_REQUEST;
```

The next step is sending the above structure to the debuggee when debuggee is paused and waiting for new command on **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_MODE_STEP` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends a pause packet with the following type.

```c
DEBUGGEE_PAUSING_REASON_DEBUGGEE_STEPPED
```

The following function is responsible for sending breakpoint buffers in the debugger.

```c
BOOLEAN KdSendStepPacketToDebuggee(DEBUGGER_REMOTE_STEPPING_REQUEST StepRequestType);
```

### **Remarks**

This command will set a **trap flag** in debuggee and continue all the cores. After executing one instruction, it halts the debuggee again.

If the target instruction is a call instruction, it configures the first hardware debug register breakpoint to the instruction after that call. After that, it continues the debuggee and waits for the call to be returned and the next instruction \(after the call\) to be executed.

All cores and threads \(except the currently executing thread\) find a chance to be executed between each step in this type of stepping.

### Requirements

None

### Related

[t \(step-in\)](https://docs.hyperdbg.com/commands/debugging-commands/t)

[i \(instrument step-in\)](https://docs.hyperdbg.com/commands/debugging-commands/i)



