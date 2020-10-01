---
description: Description about log the state mechanism
---

# Assumptions & Evaluations

The following articles describe the **HyperDbg**'s _Log the State_ mechanism.

The evaluation engine is open-source and it is available at the [evaluation](https://github.com/HyperDbg/evaluation) repository.

### Description

**HyperDbg** uses a MASM-like syntax to evaluate log the state's expressions.

### Pseudo Registers

Here are the currently supported pseudo-registers supported by Log the State.

| Pseudo-register | Description |
| :--- | :--- |
| **$pid** | The process ID \(PID\) of the current process. |
| **$proc** | The address of the current process \(that is, the address of the`EPROCESS` block\). |
| **$tid** | The thread ID for the current thread. |
| **$thread** | The address of the current thread. In kernel-mode debugging, this address is the address of the `ETHREAD` block. |
| **$peb** | The address of the process environment block \(PEB\) of the current process. |
| **$teb** | The address of the thread environment block \(TEB\) of the current thread. |
| **$ip** | The instruction pointer register \(rip\). |

 

### Number Prefixes

By default, **HyperDbg** interprets the numbers as hex \(base 16\), if you want to specify other forms of a number you should use MASM prefixes. In all MASM expressions, numeric values are interpreted as numbers in the current radix \(16, 10, or 8\). You can override the default radix by specifying the 0x prefix \(hexadecimal\), the 0n prefix \(decimal\), the 0t prefix \(octal\), or the 0y prefix \(binary\).



