---
description: Description of 'print' function in HyperDbg Scripts
---

# print

### Function

> print

### Syntax

> print\( Expression \);

### Parameters

**\[Expression\]**

         A [MASM-like expression](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations) to evaluate.

### Description

Evaluates and prints a MASM expression.

### Examples

`print(@rcx);`

Print **rcx** register.

`print(dq(@rcx));`

Print data as an 8-byte hex, pointed by **rcx** register.

`print($proc+@rdx);`

Print value pointed by `$proc+@rdx`  which **$proc** is equivalent to current `_EPROCESS`  added to the **rdx** register.

{% hint style="success" %}
You can see more examples [here](https://docs.hyperdbg.com/commands/scripting-language/examples/view-system-state).
{% endhint %}

### **Remarks**

You should consider avoiding ["unsafe" behavior](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior) in your expressions.

### Related

[printf](https://docs.hyperdbg.com/commands/scripting-language/functions/exports/printf)



