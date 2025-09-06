---
description: Description of the 'print' command in HyperDbg.
---

# print (evaluate and print expression in debuggee)

### Command

> print

### Syntax

> print \[Expression (string)]

### Description

Shows the result of an expression that will be executed in the remote debuggee.

### Parameters

**\[Expression (string)]**

The expression is based on HyperDbg's [scripting language](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations).

### Examples

The following command shows the `@rax` register in the debuggee.

```diff
0: kHyperDbg> print @rax
```

The following command shows the data as an 8-byte hex, pointed by the `@rcx` register.

```diff
0: kHyperDbg> print dq(@rcx))
```

The following command shows the value pointed by `$proc+@rdx` which **$proc** is equivalent to current `_EPROCESS` added to the **rdx** register.

```diff
0: kHyperDbg> print $proc+@rdx
```

The following command shows the value of an address, which first, **rax** register is added with **0xa0** constant then a dereference occurs and the target is shown as a QWORD hex.

```diff
0: kHyperDbg> print poi(@rax+a0)
```

### IOCTL

For using this command, you use the same SDK function as the '[.formats](https://docs.hyperdbg.org/commands/meta-commands/.formats#ioctl)' command.&#x20;

### Remarks

This command is exactly like `print(expr);` in script engine, except that HyperDbg automatically adds `print(` and `);`.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

None
