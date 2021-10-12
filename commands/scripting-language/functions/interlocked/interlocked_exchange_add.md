---
description: Description of 'interlocked_exchange_add' function in HyperDbg Scripts
---

# interlocked_exchange_add

## Function

> interlocked_exchange_add

## Syntax

> interlocked_exchange_add( \*Variable, Expression );

## Parameters

**\[\*Variable (Addend)]**

A reference to a variable (most of the time a global variable) to add the value of the resulting instruction to it.

**\[Expression (Value)]**

The value which is added to the global variable.

## Description

Performs an atomic addition of two values.

## Return value

The function returns the initial value of the **`GlobalVariable(Addend)`** parameter.

## Examples

`Result = interlocked_exchange_add(.my_gloabl_counter, 0x55);`

Adds the value (`0x55`) to the global variable and saves the initial value of the `.my_gloabl_counter` into a local variable named `Result`.

## **Remarks**

You can also perform the subtraction by addition (using the 2's-complement system).

## Related

[interlocked_compare_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_compare_exchange)

[interlocked_increment](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_increment)

[interlocked_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange)

[interlocked_decrement](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_decrement)
