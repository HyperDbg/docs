---
description: Description of 'settings' command in HyperDbg.
---

# settings \(configures different options and preferences\)

### Command

> settings

### Syntax

> settings \[option name\] \[value \(name \| hex value \| on \| off\)\]

### Description

This command queries or changes the value of options and preferences.

### Parameters

**\[option name\]**

Name of the option.

**\[value \(name \| hex value \| on \| off\)\] \(optional\)**

Target value to modify the option.

{% hint style="success" %}
Each option has its own value; for example, some options might be on or off, and others might be custom numbers or names.
{% endhint %}

## Options

| Options | Values | Default Value |
| :--- | :--- | :--- |
| **autoflush** | on \| off | off |
| **autounpause** | on \| off | on |
| **syntax** | intel \| att \| masm | intel |

**autoflush :** if you turn **autoflush** on, after each disabling or clearing an event using the '[events](https://docs.hyperdbg.org/commands/debugging-commands/events)' command, all the possible pending buffers and messages from **all the commands \(not just the command that you disabled or removed\)** that are stored to be received by the user-mode from the kernel-mode and when you press '[g](https://docs.hyperdbg.org/commands/debugging-commands/g)', you no longer see any results from previous commands; however, some commands might continue generating new messages, and those new messages won't be removed.

You can also perform the **autoflush** manually by running the '[flush](https://docs.hyperdbg.org/commands/debugging-commands/flush)' command.

**autounpause** : if you turn **autounpause** on, then if you press CTRL+C or run the '[pause](https://docs.hyperdbg.org/commands/debugging-commands/pause)' command to break to the debugger, every new event will automatically turn off your break, and you'll start receiving events and messages from the kernel-mode buffers. However, if you turn it off, then you won't receive kernel buffers and messages when you invoke a new event as the debugger will remain in the paused state \(for example, paused because of '[pause](https://docs.hyperdbg.org/commands/debugging-commands/pause)' command or CTRL+C\), in this case, you can resume receiving messages from the kernel-mode buffer by running '[g](https://docs.hyperdbg.org/commands/debugging-commands/g)' command.

**syntax** : specifies the syntax of disassembler for '[!u and !u2](https://docs.hyperdbg.org/commands/extension-commands/u)' commands.

### Examples

The following command shows the state of **`autounpause`** option.

```diff
HyperDbg> settings autounpause
auto-unpause is enabled
```

The following command turns **`autounpause`** on.

```diff
HyperDbg> settings autounpause on
set auto-unpause to enabled
```

The following command turns **`autounpause`** off.

```text
HyperDbg> settings autounpause off
set auto-unpause to disabled
```

The following command shows the different **syntax** used in the disassembler. You can choose your favorite **syntax** to show in '[!u and !u2' ](https://docs.hyperdbg.org/commands/extension-commands/u)commands.

```c
HyperDbg> settings syntax intel
set syntax to intel


HyperDbg >u fffff804`2d16f010
fffff804`2d16f010 48 89 5C 24 08                      mov qword ptr ss:[rsp+0x08], rbx
fffff804`2d16f015 48 89 6C 24 10                      mov qword ptr ss:[rsp+0x10], rbp
fffff804`2d16f01a 48 89 74 24 18                      mov qword ptr ss:[rsp+0x18], rsi
fffff804`2d16f01f 57                                  push rdi
fffff804`2d16f020 41 56                               push r14
fffff804`2d16f022 41 57                               push r15
fffff804`2d16f024 48 83 EC 30                         sub rsp, 0x30
fffff804`2d16f028 65 48 8B 04 25 20 00 00 00          mov rax, qword ptr gs:[0x0000000000000020]
fffff804`2d16f031 33 DB                               xor ebx, ebx
fffff804`2d16f033 44 0F B7 3D C5 3F 20 00             movzx r15d, word ptr ds:[0xFFFFF8042D373000]
fffff804`2d16f03b 41 8B E8                            mov ebp, r8d
fffff804`2d16f03e 48 8B F2                            mov rsi, rdx
fffff804`2d16f041 89 5C 24 68                         mov dword ptr ss:[rsp+0x68], ebx
fffff804`2d16f045 8B F9                               mov edi, ecx
fffff804`2d16f047 4C 8B 88 C0 00 00 00                mov r9, qword ptr ds:[rax+0xC0]


HyperDbg> settings syntax att
set syntax to at&t

HyperDbg> u fffff804`2d16f010
fffff804`2d16f010 48 89 5C 24 08                      movq %rbx, %ss:0x08(%rsp)
fffff804`2d16f015 48 89 6C 24 10                      movq %rbp, %ss:0x10(%rsp)
fffff804`2d16f01a 48 89 74 24 18                      movq %rsi, %ss:0x18(%rsp)
fffff804`2d16f01f 57                                  push %rdi
fffff804`2d16f020 41 56                               push %r14
fffff804`2d16f022 41 57                               push %r15
fffff804`2d16f024 48 83 EC 30                         sub $0x30, %rsp
fffff804`2d16f028 65 48 8B 04 25 20 00 00 00          movq %gs:0x0000000000000020, %rax
fffff804`2d16f031 33 DB                               xor %ebx, %ebx
fffff804`2d16f033 44 0F B7 3D C5 3F 20 00             movzxw %ds:0xFFFFF8042D373000, %r15d
fffff804`2d16f03b 41 8B E8                            mov %r8d, %ebp
fffff804`2d16f03e 48 8B F2                            mov %rdx, %rsi
fffff804`2d16f041 89 5C 24 68                         movl %ebx, %ss:0x68(%rsp)
fffff804`2d16f045 8B F9                               mov %ecx, %edi
fffff804`2d16f047 4C 8B 88 C0 00 00 00                movq %ds:0xC0(%rax), %r9


HyperDbg> settings syntax masm
set syntax to masm

HyperDbg >u fffff804`2d16f010
fffff804`2d16f010 48 89 5C 24 08                      mov qword ptr ss:[rsp+8h], rbx
fffff804`2d16f015 48 89 6C 24 10                      mov qword ptr ss:[rsp+10h], rbp
fffff804`2d16f01a 48 89 74 24 18                      mov qword ptr ss:[rsp+18h], rsi
fffff804`2d16f01f 57                                  push rdi
fffff804`2d16f020 41 56                               push r14
fffff804`2d16f022 41 57                               push r15
fffff804`2d16f024 48 83 EC 30                         sub rsp, 30h
fffff804`2d16f028 65 48 8B 04 25 20 00 00 00          mov rax, qword ptr gs:[$+20h]
fffff804`2d16f031 33 DB                               xor ebx, ebx
fffff804`2d16f033 44 0F B7 3D C5 3F 20 00             movzx r15d, word ptr ds:[$+203FCDh]
fffff804`2d16f03b 41 8B E8                            mov ebp, r8d
fffff804`2d16f03e 48 8B F2                            mov rsi, rdx
fffff804`2d16f041 89 5C 24 68                         mov dword ptr ss:[rsp+68h], ebx
fffff804`2d16f045 8B F9                               mov edi, ecx
fffff804`2d16f047 4C 8B 88 C0 00 00 00                mov r9, qword ptr ds:[rax+C0h]
```

### IOCTL

None

### Remarks

**autounpause** and **autoflush** do not change anything in [debugger mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode). It is because the buffers are not accumulated and passed instantly in this mode; thus, there is nothing to flush.

### Requirements

None

### Related

None

