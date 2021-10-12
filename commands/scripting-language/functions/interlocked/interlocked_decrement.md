---
description: Description of 'interlocked_decrement' function in HyperDbg Scripts
---

# interlocked_decrement

## Function

> interlocked_decrement

## Syntax

> interlocked_decrement( \*Variable );

## Parameters

**\[\*Variable (Addend)]**

A reference to a variable (most of the time a global variable) to be subtracted by `1`.

## Description

Decrements (decreases by one) the value of the specified global variable as an atomic operation.

## Return value

The function returns the resulting decremented value.

## Examples

`Result = interlocked_decrement(.my_gloabl_counter);`

Decrements a global variable by **`1`** and saves the results into a local variable named `Result`.

## **Remarks**

None

## Related

[interlocked_compare_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_compare_exchange)

[interlocked_increment](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_increment)

[interlocked_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange)

[interlocked_exchange_add](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange_add)
