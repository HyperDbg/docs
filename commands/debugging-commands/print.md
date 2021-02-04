---
description: Description of 'print' command in HyperDbg.
---

# print \(evaluate and print expression in debuggee\)

### Command

> print

### Syntax

> print \[expression\]

### Description

Shows the result of an expression that will be executed in the remote debuggee.

### Parameters

**\[expression\]**

          The expression based on HyperDbg's [scripting language](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations).

### Examples

The following command shows the `@rax` ****register in the debuggee.

```diff
HyperDbg> print @rax
```

The following command shows the data as an 8-byte hex, pointed by the `@rcx` register.

```diff
HyperDbg> print dq(@rcx))
```

The following command shows the value pointed by `$proc+@rdx`  which **$proc** is equivalent to current `_EPROCESS`  added to the **rdx** register.

```diff
HyperDbg> print $proc+@rdx
```

The following command shows the value of an address, which first, **rax** register is added with **0xa0** constant then a dereference occurs and the target is shown as a QWORD hex.

```diff
HyperDbg> print poi(@rax+a0)
```

The following command shows the **$proc** \(current `_EPROCESS`\) added with **0x10**, then a dereference occurs, and the target pointer in the dereferenced location is shown as a wide-char string.

```diff
HyperDbg> print wstr(poi($proc+10))
```

The following command shows the **$proc** \(current `_EPROCESS`\) added with **0x10**, ****then a dereference occurs, and the target pointer in the dereferenced location is shown as an ASCII string.

```diff
HyperDbg> print str(poi($proc+10))
```

### IOCTL

### **Remarks**

None

### Requirements

None

### Related

None

