---
description: Description of the 'i' command in HyperDbg.
---

# i (instrumentation step-in)

### Command

> i
>
> ir

### Syntax

> i
>
> i \[Count (hex)]
>
> ir
>
> ir \[Count (hex)]

### Description

Executes a single instruction (step-in) and optionally displays the resulting values of all registers and flags. This command only works in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode).

{% hint style="success" %}
The difference between this command and the '[t](https://docs.hyperdbg.org/commands/debugging-commands/t)' command is that no other cores and other threads find a chance to be executed during the stepping process; the system is fully halted, and only the current core will execute just one instruction and halts again.
{% endhint %}

{% hint style="info" %}
This command gives you the ability to follow system-calls (SYSCALLs) and all the exceptions (including **page-faults**) from user-mode to kernel-mode and from kernel-mode to user-mode. For example, in the middle of executing one instruction in user-mode, a page-fault might happen, then if you use this command, the next instruction is in the kernel-mode **page-fault** handler. Another example, you can follow a _syscall_ from user-mode, then the next instruction is in kernel-mode **syscall handler**, and this way, you can trace the execution between different rings.
{% endhint %}

### Parameters

**\[Count (hex)] (optional)**

Count of step(s), or how many times perform the stepping operation. If you don't set this argument, then by default, the **Count** is `1`.

### Examples

If you want to instrumentation step-in one instruction.

```
0: kHyperDbg> i
fffff801`68d91267    41 5B                               pop r11
```

If you want to instrumentation step-in one instruction and view the registers.

```c
0: kHyperDbg> ir
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

If you want to instrumentation step-in for `5` instructions.

```c
0: kHyperDbg> i 5
fffff801`68d9126b    9D                                  popfq
fffff801`68d9126c    C3                                  ret
fffff801`63a12948    6A D1                               push 0xFFFFFFFFFFFFFFD1
fffff801`63a1294a    E9 B1 00 00 00                      jmp 0xFFFFF80163A12A00
fffff801`63a12b00    F6 44 24 10 01                      test byte ptr ss:[rsp+0x10], 0x01
```

### SDK

To step through the instruction (instrumentation step-in), you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_stepping_instrumentation_step_in();
```

### Remarks

This command will set a **Monitor Trap Flag** in debuggee and continue just the current executing core. After executing one instruction, it halts the debuggee again.

If the currently executing instruction is a **call** instruction, it will follow and enter the call instruction.

HyperDbg guarantees that all cores and threads won't find a chance to be executed between each step in this type of stepping.

If you load symbols and you don't want to see function names, you turn **addressconversion** off in the '[settings](https://docs.hyperdbg.org/commands/debugging-commands/settings)' command.

### Requirements

None

### Related

[p (step-over)](https://docs.hyperdbg.org/commands/debugging-commands/p)

[t (step-in)](https://docs.hyperdbg.org/commands/debugging-commands/t)

[gu (step-out or go up)](https://docs.hyperdbg.org/commands/debugging-commands/gu)
