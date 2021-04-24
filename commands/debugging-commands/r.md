---
description: Description of 'r' command in HyperDbg.
---

# r \(read or modify registers\)

### Command

> r

### Syntax

> r \[register\] \[= expr\]

### Description

Reads or modifies registers when the debuggee is paused.

### Parameters

**\[register\]**

          The register that needs to be read or modified

**\[= expr\]**

          The value or the expression that needs to be evaluated and modify the target register

{% hint style="info" %}
If you don't specify any parameters to the 'r' command, it shows all general-purpose + segment registers.
{% endhint %}

{% hint style="success" %}
The first parameter to this command is a register \(not an expression\). If you want to evaluate and see the result of registers as an expression \(e.g., `rax+rbx+rcx`\) then you can use the '[.formats](https://docs.hyperdbg.com/commands/meta-commands/.formats)' command.
{% endhint %}

### Examples

If you want to see all general purpose and segment registers.

```bash
0: kHyperDbg> r
RAX=ffff948cc1ba4780 RBX=ffff948cc02f19e0 RCX=0000000000000024
RDX=0000000000000000 RSI=0000000000000000 RDI=ffff948cc23403a0
RIP=fffff80168d91262 RSP=ffff9305483f3698 RBP=0000000000000002
R8=0000000000000000  R9=0000000000000000  R10=0000000048564653
R11=0000564d43414c4c R12=4e4f485950455256 R13=0000000000000000
R14=ffff948cc23403a0 R15=ffff948cc05556b0 IOPL=00
OF 0  DF 0  IF 1  SF  0
ZF 1  PF 1  CF 0  AXF 0
CS=0010 SS=0018 DS=002b ES=002b FS=0053 GS=002b
RFLAGS=0000000000040246
```

If you want to see one special register \(e.g., `rax`\).

```text
0: kHyperDbg> r rax
rax=ffff948cc1ba4780
```

If you want to see one special register \(e.g., `cs`\).

```text
0: kHyperDbg> r cs
cs=0000000000000010
```

If you want to change a register to a constant hex value.

```text
0: kHyperDbg> r rax = 10
```

If you want to change a register to a new value which is the result of an expression.

```text
0: kHyperDbg> r rcx = rax + rdx + 10
```

### IOCTL

This commands works over serial by sending the serial packets to the remote computer.

First of all, you should fill the following structure, set the `RegisterId` to your special register number, which is an ID from `REGS_ENUM` enum.

If you want to view all the registers, then you should set `RegisterID` to `DEBUGGEE_SHOW_ALL_REGISTERS` .

```c
typedef struct _DEBUGGEE_REGISTER_READ_DESCRIPTION {

  UINT32 RegisterID; // the number is from REGS_ENUM
  UINT64 Value;
  UINT32 KernelStatus;

} DEBUGGEE_REGISTER_READ_DESCRIPTION, *PDEBUGGEE_REGISTER_READ_DESCRIPTION;
```

The next step is sending the above structure to the debuggee when debuggee is paused and waiting for new command on **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_READ_REGISTERS` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_READING_REGISTERS
```

In the returned structure, the `KernelStatus` is filled by the kernel.

If the `KernelStatus` is `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then the operation was successful. Otherwise, the returned result is an error.

The following function is responsible for sending read register buffers in the debugger.

```c
BOOLEAN KdSendReadRegisterPacketToDebuggee(PDEBUGGEE_REGISTER_READ_DESCRIPTION RegDes);
```

If you specified `DEBUGGEE_SHOW_ALL_REGISTERS` then the debuggee sends the registers with two buffers that are appended. The first buffer is `GUEST_REGS` and the second buffer is `GUEST_EXTRA_REGISTERS`. 

The first buffer contains general-purpose registers, and the second buffer contains other registers, including segment registers.

Note that modifying registers are performed through script-engine as we might need the evaluation of expressions to get the register's value.

### **Remarks**

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

None

