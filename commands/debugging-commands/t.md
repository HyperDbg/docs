---
description: Description of the 't' command in HyperDbg.
---

# t (step-in)

### Command

> t
>
> tr

### Syntax

> t
>
> t \[Count (hex)]
>
> tr
>
> tr \[Count (hex)]

### Description

Executes a single instruction (step-in) and optionally displays the resulting values of all registers and flags.

{% hint style="success" %}
The difference between this command and the '[i](https://docs.hyperdbg.org/commands/debugging-commands/i)' command is that, in the '[i](https://docs.hyperdbg.org/commands/debugging-commands/i)' command, no other cores and other threads find a chance to be executed during the stepping process; the system is fully halted, and only the current core will execute just one instruction and halts again. However, in this command, all the threads and cores are continued until an instruction in the target thread is executed. This command won't follow the execution between different rings.
{% endhint %}

### Parameters

**\[Count (hex)] (optional)**

Count of step(s), or how many times perform the stepping operation. If you don't set this argument, then by default, the **Count** is `1`.

### Examples

If you want to step-in one instruction.

```
0: kHyperDbg> t
fffff801`68d91267    41 5B                               pop r11
```

If you want to step-in one instruction and view the registers.

```c
0: kHyperDbg> tr
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

If you want to step-in for `5` instructions.

```c
0: kHyperDbg> t 5
fffff801`68d9126b    9D                                  popfq
fffff801`68d9126c    C3                                  ret
fffff801`63a12948    6A D1                               push 0xFFFFFFFFFFFFFFD1
fffff801`63a1294a    E9 B1 00 00 00                      jmp 0xFFFFF80163A12A00
fffff801`63a12b00    F6 44 24 10 01                      test byte ptr ss:[rsp+0x10], 0x01
```

### SDK

To step through the instruction (regular step-in), you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_stepping_regular_step_in();
```

### Remarks

This command will set a **trap flag** in debuggee and continue all the cores. After executing one instruction, it halts the debuggee again.

If the currently executing instruction is a **call** instruction, it will follow and enter the call instruction.

All cores and threads (except the currently executing thread) find a chance to be executed between each step in this type of stepping.

If you load symbols and you don't want to see function names, you turn **addressconversion** off in the '[settings](https://docs.hyperdbg.org/commands/debugging-commands/settings)' command.

### Requirements

None

### Related

[p (step-over)](https://docs.hyperdbg.org/commands/debugging-commands/p)

[i (instrumentation step-in)](https://docs.hyperdbg.org/commands/debugging-commands/i)

[gu (step-out or go up)](https://docs.hyperdbg.org/commands/debugging-commands/gu)
