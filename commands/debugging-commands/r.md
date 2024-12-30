---
description: Description of the 'r' command in HyperDbg.
---

# r (read or modify registers)

### Command

> r

### Syntax

> r
>
> r \[Register (string)] \[= Expr (string)]

### Description

Reads or modifies registers when the debuggee is paused.

### Parameters

**\[Register (string)] (optional)**

The register that needs to be read or modified.

**\[= Expr (string)] (optional)**

The value or the expression that needs to be evaluated and modify the target register.

{% hint style="info" %}
If you don't specify any parameters to the 'r' command, it shows all general-purpose + segment registers.
{% endhint %}

{% hint style="success" %}
The first parameter to this command is a register (not an expression). If you want to evaluate and see the result of registers as an expression (e.g., `rax+rbx+rcx`) then you can use the '[.formats](https://docs.hyperdbg.org/commands/meta-commands/.formats)' command.
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

If you want to see one special register (e.g., `rax`).

```
0: kHyperDbg> r rax
rax=ffff948cc1ba4780
```

If you want to see one special register (e.g., `cs`).

```
0: kHyperDbg> r cs
cs=0000000000000010
```

If you want to change a register to a constant hex value.

```
0: kHyperDbg> r rax = 10
```

If you want to change a register to a new value which is the result of an expression.

```
0: kHyperDbg> r rcx = rax + rdx + 10
```

### SDK

To read all registers in the target debuggee, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_read_all_registers(GUEST_REGS * guest_registers, GUEST_EXTRA_REGISTERS * extra_registers);
```

To read a single register in the target debuggee, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_read_target_register(REGS_ENUM register_id, UINT64 * target_register);
```

To write (modify) a single register in the target debuggee, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_write_target_register(REGS_ENUM register_id, UINT64 value);
```

If you want to read all registers (based on current text messaging callback or std output), you can use the following function:

```clike
BOOLEAN
hyperdbg_u_show_all_registers();
```

If you want to read a single register (based on current text messaging callback or std output), you can use the following function:

```clike
BOOLEAN
hyperdbg_u_show_target_register(REGS_ENUM register_id);
```

### Remarks

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

None
