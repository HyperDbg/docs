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

The following command shows the `@rax` in the debuggee.

```diff
HyperDbg> print @rax
```



```diff
HyperDbg> print dq(@rcx))
```

### 

```diff
HyperDbg> print $proc+@rdx
```

### 

```diff
HyperDbg> print poi(@rax+a0)
```

### 

```diff
HyperDbg> print wstr(poi($proc+10))
```

### IOCTL

### **Remarks**

None

### Requirements

None

### Related

None

