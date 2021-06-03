---
description: Description of 'x' command in HyperDbg.
---

# x \(examine symbols and find functions and variables address\)

### Command

> x

### Syntax

> x \[module!symbol \(wildcard string\)\]

### Description

Finds the functions or variables address in the memory based on symbols.

### Parameters

**\[module!symbol \(wildcard string\)\]**

          Specifies a function or a variable name or a pattern that the symbol must contain. The _**Symbol**_ can contain a variety of wildcard characters and specifiers. For more information about the syntax, see **Remarks**.

### Examples

In the following example, we searched for the address of the `ExAllocatePoolWithTag` function in the `nt` module.

```text
HyperDbg> x nt!ExAllocatePoolWithTag
fffff803`1dbb1030  nt!ExAllocatePoolWithTag
```

In the following example, we searched for the address of functions \(and names\) which, starts with `ExAllocatePoo` in the `nt` module.

```text
0: kHyperDbg> x nt!ExAllocatePoo*
fffff803`1dbb1030  nt!ExAllocatePoolWithTag
fffff803`1d4421d0  nt!ExAllocatePoolWithQuotaTag
fffff803`1d44fde0  nt!ExAllocatePoolWithTagPriority
fffff803`1d522500  nt!ExAllocatePool
fffff803`1dbb1340  nt!ExAllocatePool3
fffff803`1dbb1280  nt!ExAllocatePool2
fffff803`1d7ad3d0  nt!ExAllocatePoolWithQuota
fffff803`1d45c5c0  nt!ExAllocatePoolEx
fffff803`1dbe9d84  nt!ExAllocatePoolSanityChecks
fffff803`1d4a1070  nt!ExAllocatePoolMm
```

### IOCTL

None

### **Remarks**

{% hint style="info" %}
The default module is **`nt`**. If you don't specify the module name, then **`nt`** is selected.
{% endhint %}

These are some examples of wildcard characters supported by this command.

| Value | Meaning |
| :--- | :--- |
| **foo** | Looks for a global symbol \(functions, variables\) named "**foo**". |
| **foo?** | Looks for a global symbol that starts with "**foo**" and contains one extra character afterward, such as "**fool**" and "**foot**". |
| **foo!bar\*** | Looks for a global symbol in "**foo**" that starts with "**bar**" and contains extra characters afterward, such as "**foo!bar1**" and "**foo!bar2**". |

### Requirements

None

### Related

None

