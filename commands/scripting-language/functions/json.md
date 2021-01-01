---
description: Description of 'Json' function in HyperDbg Scripts
---

# Json

### Function

> Json

### Syntax

> Json\( FieldName, Expression \);

### Parameters

\[FieldName \(string\)\]

         Name of the value.

\[Expression\]

         A [MASM-like expression](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations) to evaluate.

### Description

Evaluate and print MASM expression in **JSON** format.

### Examples

`json("FirstArg", str(@rcx));`

Print data as an ASCII string pointed by **rcx** register as a value whose name is `FirstArg`.

`print("Name", dq(@rcx));`

Print data as an 8-byte hex, pointed by **rcx** register as a value whose name is `Name`.

### **Remarks**

You should consider avoiding ["unsafe" behavior](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior) in your expressions.



