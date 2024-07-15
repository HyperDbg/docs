---
description: Description of the '!a' command in HyperDbg.
---

# !a (assemble physical address)

### Command

> !a

### Syntax

> !a \[Address (hex)] \[asm {AsmCmd1; AsmCmd2}]

### Description

Assembles (shows HEX byte codes) or puts the resulting instruction codes into the (physical) memory.

### Parameters

**\[Address (hex)] (optional)**

The **physical** address of where we want to start putting resulting codes into its memory.

{% hint style="info" %}
If the **Address** is empty, you can use it to only assemble instructions without modifying the memory.
{% endhint %}

**\[asm {AsmCmd1; AsmCmd2}]**

The target assembly codes.

{% hint style="danger" %}
Process ID doesn't make sense in physical memory. If you specify **pid** for physical memory, it is ignored.
{% endhint %}

### Examples

If you just want to view the result of assembly (byte codes in HEX) without modifying the memory, the following command can be used.

```
HyperDbg> !a { nop; xor rax, rax; nop }
warning, no start address provided to calculate relative asm commands

generated assembly: 5 bytes, 3 statements ==>>  nop; xor rax, rax; nop  = 90 48 31 c0 90
```

The following command is used when we want to assemble assembly codes and put the resulting bytes into the target memory at `abc1c0` .

```diff
HyperDbg> !a abc1c0 { nop; nop; nop }
generated assembly: 3 bytes, 3 statements ==>>  nop; nop; nop  = 90 90 90
successfully assembled at 0xabc1c0 address
```

### IOCTL

None

### Remarks

* By default, HyperDbg converts addresses to the object names (if the symbol for that address is available). If you want to see the address in hex format, you can turn `addressconversion` to **off** using the '[settings](https://docs.hyperdbg.org/commands/debugging-commands/settings)' command.
* To view the byte code of an assembly snippet, you can use the following command (`StartAddress` is useful when dealing with relative instructions like **JMP**).

```
 !a {jmp <nt!ExAllocatePoolWithTag+10>} [StartAddress]
```

{% hint style="success" %}
HyperDbg uses [**keystone**](https://github.com/keystone-engine/keystone) as its core assembler.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[Keystone](https://www.keystone-engine.org/)

[a (assemble virtual address)](https://docs.hyperdbg.org/commands/debugging-commands/a)

[u, u64, u2, u32 (disassemble virtual address)](https://docs.hyperdbg.org/commands/debugging-commands/u)

[!u, !u64, !u2, !u32 (disassemble physical address)](https://docs.hyperdbg.org/commands/extension-commands/u)
