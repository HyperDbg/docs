---
description: Description of the 'a' command in HyperDbg.
---

# a (assemble virtual address)

### Command

> a

### Syntax

> a \[Address (hex)] \[asm {AsmCmd1; AsmCmd2}] \[pid ProcessId (hex)]

### Description

Assembles (shows HEX byte codes) or puts the resulting instruction codes into the (virtual) memory.

### Parameters

**\[Address (hex)] (optional)**

The **virtual** address of where we want to start putting resulting codes into its memory.

{% hint style="info" %}
If the **Address** is empty, you can use it to only assemble instructions without modifying the memory.
{% endhint %}

**\[asm {AsmCmd1; AsmCmd2}]**

The target assembly codes.

**\[pid ProcessId (hex)] (optional)**

The Process ID that's in the hex format is what we want to put the memory to its context (**cr3**).

{% hint style="info" %}
If you don't specify the **pid**, then the default **pid** is the current process (HyperDbg) process layout of memory.
{% endhint %}

### Examples

If you just want to view the result of assembly (byte codes in HEX) without modifying the memory, the following command can be used.

```
HyperDbg> a { nop; xor rax, rax; nop }
warning, no start address provided to calculate relative asm commands

generated assembly: 5 bytes, 3 statements ==>>  nop; xor rax, rax; nop  = 90 48 31 c0 90
```

The following command is used when we want to assemble assembly codes and put the resulting bytes into the target memory at ``fffff800`3ad6f010`` .

```diff
HyperDbg> a fffff800`3ad6f010 { nop; nop; nop }
generated assembly: 3 bytes, 3 statements ==>>  nop; nop; nop  = 90 90 90
successfully assembled at 0xfffff8003ad6f010 address
```

The following command is used when we want to assemble assembly codes and put the resulting bytes into the target memory at `nt!ExAllocatePoolWithTag` .

```diff
2: kHyperDbg> a nt!ExAllocatePoolWithTag { nop; nop; nop }
generated assembly: 3 bytes, 3 statements ==>>  nop; nop; nop  = 90 90 90
successfully assembled at 0xfffff804136acc80 address
```

The following command is used when we want to assemble assembly codes and put the resulting bytes into the target memory at `nt!ExAllocatePoolWithTag+5` .

```diff
2: kHyperDbg> a nt!ExAllocatePoolWithTag+5 { nop; nop; nop }
generated assembly: 3 bytes, 3 statements ==>>  nop; nop; nop  = 90 90 90
successfully assembled at 0xfffff804136acc85 address
```

You can also write multiple lines of assembly codes and use function names in your assembly:

```
2: kHyperDbg> a nt!ExAllocatePoolWithTag {
>       add DWORD PTR [<nt!ExAllocatePoolWithTag+10+@rax>], 99;
>       nop;
>       nop
>       }
generated assembly: 12 bytes, 3 statements ==>>         add DWORD PTR [0xfffff804138cecd4], 99; nop;    nop      = 81 05 4a 20 22 00 99 00 00 00 90 90
successfully assembled at 0xfffff804136acc80 address
```

### SDK

To assemble a buffer, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_assemble(const CHAR * assembly_code, UINT64 start_address, PVOID buffer_to_store_assembled_data, UINT32 buffer_size);
```

To get the length of the assembly code, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_assemble_get_length(const CHAR * assembly_code, UINT64 start_address, UINT32 * length);
```

### Remarks

* By default, HyperDbg converts addresses to the object names (if the symbol for that address is available). If you want to see the address in hex format, you can turn `addressconversion` to **off** using the '[settings](https://docs.hyperdbg.org/commands/debugging-commands/settings)' command.
* To view the byte code of an assembly snippet, you can use the following command (`StartAddress` is useful when dealing with relative instructions like **JMP**).

```
 a {jmp <nt!ExAllocatePoolWithTag+10>} [StartAddress]
```

{% hint style="success" %}
HyperDbg uses [**keystone**](https://github.com/keystone-engine/keystone) as its core assembler.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[Keystone](https://www.keystone-engine.org/)

[!a (assemble physical address)](https://docs.hyperdbg.org/commands/extension-commands/a)

[u, u64, u2, u32 (disassemble virtual address)](https://docs.hyperdbg.org/commands/debugging-commands/u)

[!u, !u64, !u2, !u32 (disassemble physical address)](https://docs.hyperdbg.org/commands/extension-commands/u)
