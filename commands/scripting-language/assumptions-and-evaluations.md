---
description: >-
  Description of keywords, operators, pseudo-registers, number prefixes, and
  pre-defined functions
---

# Assumptions & Evaluations

The following articles describe **HyperDbg**'s _Script Engine_.

## Description

**HyperDbg** uses a MASM-like \(Windbg\) syntax to evaluate script expressions.

{% hint style="success" %}
**HyperDbg** script engine is case-sensitive.
{% endhint %}

## Keywords

The following keywords are valid in **Script Engine**.

| Keyword | Description |
| :--- | :--- |
| **poi** | Pointer-sized data from the specified address. |
| **hi** | High 16 bits |
| **low** | Low 16 bits |
| **db** | Low 8 bits |
| **dd** | Low 16 bits |
| **dw** | Low 32 bits |
| **dq** | 64 bits |
| **not** | Flip each and every bit |
| **neg** | True/False logic flipping |

If you want to read strings or wide-char strings, you can use [**printf**](https://docs.hyperdbg.org/commands/scripting-language/functions/printf) function.

## Operators

The following operators are supported on the script engine.

| Operator | Description |
| :--- | :--- |
| \( \) | Parentheses |
| \* / % | Arithmetic Operators |
| + - | Arithmetic Operators |
| &lt;&lt; &gt;&gt; | Shift Operators |
| & | Bitwise AND Operator |
| ^ | Bitwise XOR Operator \(exclusive OR\) |
| \| | Bitwise OR Operator |

## Registers

The following registers are supported by HyperDbg.

| Registers | Description |
| :--- | :--- |
| **@rax @eax @ax @ah @al @rcx @ecx @cx @ch @cl @rdx @edx @dx @dh @dl @rbx @ebx @bx @bh @bl @rsi @esi @si @sil @rdi @edi @di @dil** | General-purpose registers |
| **@r8 @r8d @r8w @r8h @r8l @r9 @r9d @r9w @r9h @r9l @r10 @r10d @r10w @r10h @r10l @r11 @r11d @r11w @r11h @r11l @r12 @r12d @r12w @r12h @r12l @r13 @r13d @r13w @r13h @r13l @r14 @r14d @r14w @r14h @r14l @r15 @r15d @r15w @r15h @r15l** | 64-bit mode registers |
| **@rsp @esp @sp @spl @rbp @ebp @bp @bpl** | Stack management registers |
| **@rip @eip @ip** | Program counter \(Instruction pointer\) |
| **@rflags @eflags @flags** | Flags register |
| **@ds @es @cs @ss** | Segment registers |
| **@fs @gs** | Segment registers \(80386\) |
| **@cr0 @cr2 @cr3 @cr4 @cr8** | Control registers |
| **@gdtr @ldtr @tr** | Memory management registers |
| **@idtr** | Interrupt descriptor register |
| **@dr0 @dr1 @dr2 @dr3 @dr6 @dr7** | Debug registers |
| **Model-/machine-specific registers \(MSRs\)** | See the '[rdmsr](https://docs.hyperdbg.org/commands/debugging-commands/rdmsr)' and '[wrmsr](https://docs.hyperdbg.org/commands/debugging-commands/wrmsr)' commands for more information. |

Each flag of **RFLAGS** is also supported in HyperDbg.

| **Bit \#** | **Mask** | **Register Abbreviation** | **Description** | **Category** |
| :--- | :--- | :--- | :--- | :--- |
| **FLAGS** |  |  |  |  |
| 0 | 0x0001 | **@cf** | Carry flag | Status |
| 1 | 0x0002 | **\_** | Reserved, always 1 in **EFLAGS** | **\_** |
| 2 | 0x0004 | **@pf** | Parity flag | Status |
| 3 | 0x0008 | **\_** | Reserved | **\_** |
| 4 | 0x0010 | **@af** | Adjust flag | Status |
| 5 | 0x0020 | **\_** | Reserved | **\_** |
| 6 | 0x0040 | **@zf** | Zero flag | Status |
| 7 | 0x0080 | **@sf** | Sign flag | Status |
| 8 | 0x0100 | **@tf** | Trap flag \(single step\) | Control |
| 9 | 0x0200 | **@if** | Interrupt enable flag | Control |
| 10 | 0x0400 | **@df** | Direction flag | Control |
| 11 | 0x0800 | **@of** | Overflow flag | Status |
| 12-13 | 0x3000 | **@iopl** | I/O privilege level \(286+ only\), always 1 on 8086 and 186 | System |
| 14 | 0x4000 | **@nt** | Nested task flag \(286+ only\),  always 1 on 8086 and 186 | System |
| 15 | 0x8000 | **\_** | Reserved,  always 1 on 8086 and 186,  always 0 on later models | **\_** |
| **EFLAGS** |  |  |  |  |
| 16 | 0x0001 0000 | **@rf** | Resume flag \(386+ only\) | System |
| 17 | 0x0002 0000 | **@vm** | Virtual 8086 mode flag \(386+ only\) | System |
| 18 | 0x0004 0000 | **@ac** | Alignment check \(486SX+ only\) | System |
| 19 | 0x0008 0000 | **@vif** | Virtual interrupt flag \(Pentium+\) | System |
| 20 | 0x0010 0000 | **@vip** | Virtual interrupt pending \(Pentium+\) | System |
| 21 | 0x0020 0000 | **@id** | Able to use CPUID instruction \(Pentium+\) | System |
| 22‑31 | 0xFFC0 0000 | **\_** | Reserved | System |
| **RFLAGS** |  |  |  |  |
| 32‑63 | 0xFFFF FFFF… 0000 0000 | Reserved | **\_** | **\_** |

## Pseudo-registers

Here are the currently supported pseudo-registers supported by the script engine.

| Pseudo-register | Description |
| :--- | :--- |
| **$pid** | The process ID \(PID\) of the current process. |
| **$proc** | The address of the current process \(that is, the address of the `nt!_EPROCESS` block\). |
| **$pname** | A pointer to the character array of the process name. \(It is a 16-byte long array in kernel-mode\). |
| **$tid** | The thread ID for the current thread. |
| **$core** | The core ID for the current core. |
| **$thread** | The address of the current thread. In kernel-mode debugging, this address is the address of the `nt!_ETHREAD` block. |
| **$peb** | The address of the process environment block \(PEB\) of the current process. |
| **$teb** | The address of the thread environment block \(TEB\) of the current thread. |
| **$ip** | The instruction pointer register \(rip\). |
| **$buffer** | The pre-allocated buffer if the user requests a safe buffer. |
| **$context** | The context of the triggered event \(It has a different meaning in each event\). |

Some of the above pseudo-registers are not supported either in kernel-mode or user-mode. If you use these registers, then it returns `NULL`.

## Number Prefixes

By default, **HyperDbg** interprets the numbers as hex \(base 16\). If you want to specify other forms of a number, you should use MASM prefixes. In all MASM expressions, numeric values are interpreted as numbers in the current radix \(16, 10, or 8\). You can override the default radix by specifying the 0x prefix \(hexadecimal\), the 0n prefix \(decimal\), the 0t prefix \(octal\), or the 0y prefix \(binary\).

## Comments

HyperDbg's comments are like C comments.

A comment starts with a slash asterisk `/*` and ends with an asterisk slash `*/` and can be anywhere in your program. Comments can span several lines within your C program.

```text
/* comment goes here */
```

OR

```text
/*
 * comment goes here
 */
```

You can create a comment on a single line.

```text
// comment goes here
```

## Functions

The following functions are supported in **Script Engine**.

| Function | Description |
| :--- | :--- |
| **print** | Print the result of an expression. [Read more... ](https://docs.hyperdbg.org/commands/scripting-language/functions/exports/print) |
| **printf** | Print the result in a printf-style format. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/exports/printf) |
| **eb, ed, eq** | Modify memory safely. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/eb-ed-eq) |
| **pause** | Halt the system and give control to the debugger. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/debugger/pause) |
| **enable\_event** | Enable an event. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/events/enable_event) |
| **disable\_event** | Disable an event. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/events/disable_event) |
| **check\_address** | Check if an address is valid and safe to access or not. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/check_address) |
| **strlen** | Count the length of ASCII strings. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/strlen) |
| **wcslen** | Count the length of wide-char strings. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/strings/wcslen) |
| **spinlock\_lock** | Lock an spinlock. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock) |
| **spinlock\_lock\_custom\_wait** | Lock an spinlock with a pause timer. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock_custom_wait) |
| **spinlock\_unlock** | Unlock an spinlock. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_unlock) |
| **interlocked\_compare\_exchange** | Perform an atomic compare-and-exchange operation on the specified values. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_compare_exchange) |
| **interlocked\_decrement** | Decrement \(decrease by one\) the value of the variable as an atomic operation. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_decrement) |
| **interlocked\_exchange** | Sets a variable to the specified value as an atomic operation. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange) |
| **interlocked\_exchange\_add** | Performs an atomic addition of two values. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange_add) |
| **interlocked\_increment** | Increment \(increase by one\) the value of the variable as an atomic operation. [Read more...](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_increment) |

