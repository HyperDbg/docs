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

          Specifies a function or variable name or a pattern that the symbol must contain. _**Symbol**_ can contain a variety of wildcard characters and specifiers. For more information about the syntax, see [Remarks](https://docs.hyperdbg.com/commands/debugging-commands/x#remarks).

### Examples

### IOCTL

None

### **Remarks**

These are some examples for wildcard characters supported by this command.

| Value | Meaning |
| :--- | :--- |
| **foo** | Looks for a global symbol \(functions, variables\) named "**foo**". |
| **foo?** | Looks for a global symbol that starts with "**foo**" and contains one extra character afterward, such as "**fool**" and "**foot**". |
| **foo\*!bar** | Looks in every loaded module that starts with the text "**foo**" for a symbol called "**bar**". It could find matches such as these, "**foot!bar**", "**footlocker!bar**", and "**fool!bar**". |
| **\*!\*** | It will enumerate every symbol in every loaded module. |

### Requirements

None

### Related

None

