---
description: Description of 'Print' function in HyperDbg Scripts
---

# Print

### Function

> Print

### Syntax

> Print\( Expression \);

### Parameters

\[Expression\]

         A [MASM-like expression](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations) to evaluate.

### Description

Evaluate and print a MASM expression.

### Examples

`print(str(@rcx));`

Print data as an ASCII string pointed by **rcx** register.

`print(dq(@rcx));`

Print data as an 8-byte hex, pointed by **rcx** register.

`print($proc+@rdx);`

Print value pointed by `$proc+@rdx`  which **$proc** is equivalent to current `_EPROCESS`  added to the **rdx** register.

{% hint style="success" %}
You can see more examples [here](https://docs.hyperdbg.com/commands/scripting-language/examples/view-system-state).
{% endhint %}

### **Remarks**

You should consider avoiding ["unsafe" behavior](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior) in your expressions.



