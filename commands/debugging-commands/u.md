---
description: Description of 'u, u2' command in HyperDbg.
---

# u, u2 (disassemble virtual address)

### Command

> u
>
> u2

### Syntax

> u \[address] l \[length (hex)] pid \[process id (hex)]
>
> u2 \[address] l \[length (hex)] pid \[process id (hex)]

{% hint style="info" %}
**u** disassembles as x64, and **u2** disassembles as x86.
{% endhint %}

### Description

Shows the assembly regarding memory content at the **virtual** address hex form.

### Parameters

**\[Address]**

The **virtual** address of where we want to start to disassemble its memory.

**l \[Length] (optional)**

The length (byte) in hex format.

**pid \[process id] (optional)**

The process ID that's in the hex format is what we want to see the memory from its context (**cr3**).

{% hint style="info" %}
If you don't specify the pid, then the default pid is the current process (HyperDbg) process layout of memory.
{% endhint %}

### Examples

The following command is used when we want to disassemble the content of memory (x64) at ``fffff800`3ad6f010`` with length of `0x50` bytes from the memory layout view of process (`4` a.k.a. system process).

```diff
HyperDbg> u fffff800`3ad6f010 l 50 pid 4
fffff800`3ad6f010 48 89 5C 24 08                      mov qword ptr ss:[rsp+0x08], rbx
fffff800`3ad6f015 48 89 6C 24 10                      mov qword ptr ss:[rsp+0x10], rbp
fffff800`3ad6f01a 48 89 74 24 18                      mov qword ptr ss:[rsp+0x18], rsi
fffff800`3ad6f01f 57                                  push rdi
fffff800`3ad6f020 41 56                               push r14
fffff800`3ad6f022 41 57                               push r15
fffff800`3ad6f024 48 83 EC 30                         sub rsp, 0x30
fffff800`3ad6f028 65 48 8B 04 25 20 00 00 00          mov rax, qword ptr gs:[0x0000000000000020]
fffff800`3ad6f031 33 DB                               xor ebx, ebx
fffff800`3ad6f033 44 0F B7 3D C5 3F 20 00             movzx r15d, word ptr ds:[0xFFFFF8003AF73000]
fffff800`3ad6f03b 41 8B E8                            mov ebp, r8d
fffff800`3ad6f03e 48 8B F2                            mov rsi, rdx
fffff800`3ad6f041 89 5C 24 68                         mov dword ptr ss:[rsp+0x68], ebx
fffff800`3ad6f045 8B F9                               mov edi, ecx
fffff800`3ad6f047 4C 8B 88 C0 00 00 00                mov r9, qword ptr ds:[rax+0xC0]
fffff800`3ad6f04e 45 0F B7 B1 92 00 00 00             movzx r14d, word ptr ds:[r9+0x92]
fffff800`3ad6f056 41 8B C6                            mov eax, r14d
fffff800`3ad6f059 44 8B C8                            mov r9d, eax
fffff800`3ad6f05c 89 5C 24 20                         mov dword ptr ss:[rsp+0x20], ebx
```

The following example shows the assembly content (x64) of memory at `nt!ExAllocatePoolWithTag` from current process memory layout.

```diff
HyperDbg> u nt!ExAllocatePoolWithTag
fffff801`639b1030    48 89 5C 24 08                      mov qword ptr ss:[rsp+0x08], rbx
fffff801`639b1035    48 89 6C 24 10                      mov qword ptr ss:[rsp+0x10], rbp
fffff801`639b103a    48 89 74 24 18                      mov qword ptr ss:[rsp+0x18], rsi
fffff801`639b103f    57                                  push rdi
fffff801`639b1040    41 56                               push r14
fffff801`639b1042    41 57                               push r15
fffff801`639b1044    48 83 EC 30                         sub rsp, 0x30
fffff801`639b1048    65 48 8B 04 25 20 00 00 00          mov rax, qword ptr gs:[0x0000000000000020]
fffff801`639b1051    45 8B F0                            mov r14d, r8d
fffff801`639b1054    44 0F B7 3D A4 8F 34 00             movzx r15d, word ptr ds:[0xFFFFF80163CFA000]
fffff801`639b105c    48 8B EA                            mov rbp, rdx
fffff801`639b105f    8B F1                               mov esi, ecx
fffff801`639b1061    4C 8B 88 C0 00 00 00                mov r9, qword ptr ds:[rax+0xC0]
fffff801`639b1068    41 0F B7 B9 92 00 00 00             movzx edi, word ptr ds:[r9+0x92]
```

The following example shows the assembly content (x64) of memory at `nt!ExAllocatePoolWithTag+5` from current process memory layout.

```diff
HyperDbg> u nt!ExAllocatePoolWithTag+5
fffff801`639b1035    48 89 6C 24 10                      mov qword ptr ss:[rsp+0x10], rbp
fffff801`639b103a    48 89 74 24 18                      mov qword ptr ss:[rsp+0x18], rsi
fffff801`639b103f    57                                  push rdi
fffff801`639b1040    41 56                               push r14
fffff801`639b1042    41 57                               push r15
fffff801`639b1044    48 83 EC 30                         sub rsp, 0x30
fffff801`639b1048    65 48 8B 04 25 20 00 00 00          mov rax, qword ptr gs:[0x0000000000000020]
fffff801`639b1051    45 8B F0                            mov r14d, r8d
fffff801`639b1054    44 0F B7 3D A4 8F 34 00             movzx r15d, word ptr ds:[0xFFFFF80163CFA000]
fffff801`639b105c    48 8B EA                            mov rbp, rdx
fffff801`639b105f    8B F1                               mov esi, ecx
fffff801`639b1061    4C 8B 88 C0 00 00 00                mov r9, qword ptr ds:[rax+0xC0]
fffff801`639b1068    41 0F B7 B9 92 00 00 00             movzx edi, word ptr ds:[r9+0x92]
fffff801`639b1070    0F BA EF 1F                         bts edi, 0x1F
```

The following example shows the assembly content (x64) of memory at ``fffff800`3ad6f010`` from current process memory layout.

```diff
HyperDbg> u fffff800`3ad6f010
fffff800`3ad6f010 48 89 5C 24 08                      mov qword ptr ss:[rsp+0x08], rbx
fffff800`3ad6f015 48 89 6C 24 10                      mov qword ptr ss:[rsp+0x10], rbp
fffff800`3ad6f01a 48 89 74 24 18                      mov qword ptr ss:[rsp+0x18], rsi
fffff800`3ad6f01f 57                                  push rdi
fffff800`3ad6f020 41 56                               push r14
fffff800`3ad6f022 41 57                               push r15
fffff800`3ad6f024 48 83 EC 30                         sub rsp, 0x30
fffff800`3ad6f028 65 48 8B 04 25 20 00 00 00          mov rax, qword ptr gs:[0x0000000000000020]
fffff800`3ad6f031 33 DB                               xor ebx, ebx
fffff800`3ad6f033 44 0F B7 3D C5 3F 20 00             movzx r15d, word ptr ds:[0xFFFFF8003AF73000]
fffff800`3ad6f03b 41 8B E8                            mov ebp, r8d
fffff800`3ad6f03e 48 8B F2                            mov rsi, rdx
fffff800`3ad6f041 89 5C 24 68                         mov dword ptr ss:[rsp+0x68], ebx
fffff800`3ad6f045 8B F9                               mov edi, ecx
fffff800`3ad6f047 4C 8B 88 C0 00 00 00                mov r9, qword ptr ds:[rax+0xC0]
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER_READ_MEMORY` , you have to send it in the following structure.

```c
typedef struct _DEBUGGER_READ_MEMORY {

    UINT32 Pid; // Read from cr3 of what process
    UINT64 Address;
    UINT32 Size;
    DEBUGGER_READ_MEMORY_TYPE MemoryType;
    DEBUGGER_READ_READING_TYPE ReadingType;

} DEBUGGER_READ_MEMORY, * PDEBUGGER_READ_MEMORY;
```

Where `Pid` is the process id, `Address` is the target location address and `size` is the length of the byte that you need to read.

`MemoryType`is either **virtual** or **physical**.

```c
typedef enum _DEBUGGER_READ_MEMORY_TYPE { DEBUGGER_READ_PHYSICAL_ADDRESS, DEBUGGER_READ_VIRTUAL_ADDRESS } DEBUGGER_READ_MEMORY_TYPE;
```

`ReadingType` is either from the **kernel** or from the **vmx root**. Currently, only the reading from the kernel is implemented.

```c
typedef enum _DEBUGGER_READ_READING_TYPE { READ_FROM_KERNEL, READ_FROM_VMX_ROOT } DEBUGGER_READ_READING_TYPE;
```

If you don't want to read from the kernel directly, use the following **HyperDbg Routine**.

```c
void HyperDbgReadMemoryAndDisassemble(DEBUGGER_SHOW_MEMORY_STYLE Style, UINT64 Address,
                        DEBUGGER_READ_MEMORY_TYPE MemoryType,
                        DEBUGGER_READ_READING_TYPE ReadingType, UINT32 Pid,
                        UINT Size);
```

The above function fills the IOCTL structure and shows the memory content. It is also able to disassemble the memory. You can specify one of the following `styles` to show the memory.

```c
typedef enum _DEBUGGER_SHOW_MEMORY_STYLE { DEBUGGER_SHOW_COMMAND_DISASSEMBLE64, DEBUGGER_SHOW_COMMAND_DISASSEMBLE32, DEBUGGER_SHOW_COMMAND_DB, DEBUGGER_SHOW_COMMAND_DC, DEBUGGER_SHOW_COMMAND_DQ, DEBUGGER_SHOW_COMMAND_DD } DEBUGGER_SHOW_MEMORY_STYLE;
```

For disassembling, use the `DEBUGGER_SHOW_COMMAND_DISASSEMBLE64` as the `Style` for x64 disassembling, and for disassembling x86, use the `DEBUGGER_SHOW_COMMAND_DISASSEMBLE32`.

In the debugger mode, HyperDbg uses the exact same structure, you should send the above structure over serial to the debuggee which is paused in **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_READ_MEMORY` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_READING_MEMORY
```

The following function is responsible for sending reading memory in the debugger.

```c
BOOLEAN KdSendReadMemoryPacketToDebuggee(PDEBUGGER_READ_MEMORY ReadMem);
```

### Remarks

* If you don't specify the length, the default length for HyperDbg is 0x40 Bytes.
* By default, HyperDbg converts addresses to the object names (if symbol for that address is available), if you want to see address in hex format, you can turn add.

{% hint style="warning" %}
Please note that you should specify a space between 'l' and the length for HyperDbg. For example, 'l10' is invalid, but 'l 10' is valid. (It's opposed to windbg).
{% endhint %}

{% hint style="success" %}
HyperDbg uses [Zydis](https://zydis.re) as its core disassembler.
{% endhint %}

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[Zydis](https://zydis.re)

[!u (disassemble physical address)](https://docs.hyperdbg.org/commands/extension-commands/u)
