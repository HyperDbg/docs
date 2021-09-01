---
description: >-
  Description of keywords, operators, pseudo-registers, number prefixes, and
  pre-defined functions
---

# Assumptions & Evaluations

The following articles describe **HyperDbg**'s _Script Engine_.

The evaluation engine is open-source, and it is available in the [evaluation](https://github.com/HyperDbg/evaluation) repository.

### Description

**HyperDbg** uses a MASM-like \(Windbg\) syntax to evaluate script expressions.

{% hint style="success" %}
**HyperDbg** script engine is case-sensitive.
{% endhint %}

### Keywords

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

If you want to read strings and wide-strings, you can use [**printf**](https://docs.hyperdbg.com/commands/scripting-language/functions/printf) function.

### Operators

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

### Registers

The following registers are supported by HyperDbg.

| Registers | Description |
| :--- | :--- |
| **@rax @rcx @rdx @rbx @rsi @rdi**  | General-purpose registers |
| **@r8 @r9 @r10 @r11 @r12 @r13 @r14 @r15**  | 64-bit mode registers |
| **@rsp @rbp** | Stack management registers |
| **@rip** | Program counter \(Instruction pointer\) |
| **@rflags** | Flags register |
| **@ds @es @cs @ss**  | Segment registers |
| **@fs @gs** | Segment registers \(80386\) |
| **@cr0 @cr2 @cr3 @cr4 @cr8** | Control registers |
| **@gdtr** | Memory management registers |
| **@idtr**  | Interrupt descriptor register |
| **Model-/machine-specific registers \(MSRs\)** | See the '[rdmsr](https://docs.hyperdbg.com/commands/debugging-commands/rdmsr)' and '[wrmsr](https://docs.hyperdbg.com/commands/debugging-commands/wrmsr)' commands for more information. |

### Pseudo-registers

Here are the currently supported pseudo-registers supported by the script engine.

| Pseudo-register | Description |
| :--- | :--- |
| **$pid** | The process ID \(PID\) of the current process. |
| **$proc** | The address of the current process \(that is, the address of the`EPROCESS` block\). |
| **$tid** | The thread ID for the current thread. |
| **$thread** | The address of the current thread. In kernel-mode debugging, this address is the address of the `ETHREAD` block. |
| **$peb** | The address of the process environment block \(PEB\) of the current process. |
| **$teb** | The address of the thread environment block \(TEB\) of the current thread. |
| **$ip** | The instruction pointer register \(rip\). |
| **$buffer** | The pre-allocated buffer if the user requests a safe buffer. |
| **$context** | The context of the triggered event \(It has a different meaning in each event\). |

Some of the above pseudo-registers are not supported either in kernel-mode or user-mode. If you use these registers, then it returns `NULL`.

### Number Prefixes

By default, **HyperDbg** interprets the numbers as hex \(base 16\). If you want to specify other forms of a number, you should use MASM prefixes. In all MASM expressions, numeric values are interpreted as numbers in the current radix \(16, 10, or 8\). You can override the default radix by specifying the 0x prefix \(hexadecimal\), the 0n prefix \(decimal\), the 0t prefix \(octal\), or the 0y prefix \(binary\).

### Comments

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

### Functions

The following functions are supported in **Script Engine**.

| Function | Description |
| :--- | :--- |
| **print** | Print the result of an expression. [Read more... ](https://docs.hyperdbg.com/commands/scripting-language/functions/print) |
| **printf** | Print the result in a printf-style format. [Read more...](https://docs.hyperdbg.com/commands/scripting-language/functions/printf) |
| **eb, ed, eq** | Modify memory safely. [Read more...](https://docs.hyperdbg.com/commands/scripting-language/functions/eb-ed-eq) |
| **pause** | Halt the system and give control to the debugger. [Read more...](https://docs.hyperdbg.com/commands/scripting-language/functions/pause) |
| **enable\_event** | Enable an event. [Read more...](https://docs.hyperdbg.com/commands/scripting-language/functions/enable_event) |
| **disable\_event** | Disable an event. [Read more...](https://docs.hyperdbg.com/commands/scripting-language/functions/disable_event) |
| **check\_address** | Check if an address is valid and safe to access or not. [Read more...](https://docs.hyperdbg.com/commands/scripting-language/functions/disable_event) |



