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

**\[count\]**

          Count of step\(s\), or how many times perform the stepping operation.

### Examples

If you want to step-over one instruction.

```text
HyperDbg> p
fffff801`68d91267    41 5B                               pop r11
```

If you want to step-over one instruction and view the registers.

```c
HyperDbg> pr
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
HyperDbg> p 5
fffff801`68d9126b    9D                                  popfq
fffff801`68d9126c    C3                                  ret
fffff801`63a12948    6A D1                               push 0xFFFFFFFFFFFFFFD1
fffff801`63a1294a    E9 B1 00 00 00                      jmp 0xFFFFF80163A12A00
fffff801`63a12b00    F6 44 24 10 01                      test byte ptr ss:[rsp+0x10], 0x01
```

### IOCTL

This commands works over serial by sending the serial packets to the remote computer.

First of all, you should fill the following structure, set the `StepType` to your target virtual address that you want to put a breakpoint on it, and fill `Pid` to your special process id, and/or `Tid` to your special thread id, and/or `Core` to your special core.

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

If you want your breakpoint to be triggered for all processes, threads, and cores, then choose `DEBUGGEE_BP_APPLY_TO_ALL_PROCESSES`, `DEBUGGEE_BP_APPLY_TO_ALL_THREADS`, `DEBUGGEE_BP_APPLY_TO_ALL_CORES`.

The next step is sending the above structure to the debuggee when debuggee is paused and waiting for new command on **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_BP` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_BP
```

In the returned structure, the `Result` is filled by the kernel.

If the `Result` is `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then the operation was successful. Otherwise, the returned result is an error.

The following function is responsible for sending breakpoint buffers in the debugger.

```c
BOOLEAN KdSendStepPacketToDebuggee(DEBUGGER_REMOTE_STEPPING_REQUEST StepRequestType);
```

### **Remarks**

In this command, **`pid xx`** does not mean that we will change the layout to a new process, it means that the address should be available in the current process layout but will be triggered only on the process with process id equal to **`xx`**, you can use the '[.process](https://docs.hyperdbg.com/commands/meta-commands/.process)' command to switch to a new process if you want to put a breakpoint on the layout of another process. 

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

[t \(step-in\)](https://docs.hyperdbg.com/commands/debugging-commands/t)

[i \(instrument step-in\)](https://docs.hyperdbg.com/commands/debugging-commands/i)



