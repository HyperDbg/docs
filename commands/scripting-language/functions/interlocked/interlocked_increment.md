---
description: Description of 'interlocked_increment' function in HyperDbg Scripts
---

# interlocked_increment

## Function

> interlocked_increment

## Syntax

> interlocked_increment( \*Variable );

## Parameters

**\[\*Variable (Addend)]**

A reference to a variable (most of the time a global variable) to be added by `1`.

## Description

Increments (increases by one) the value of the specified global variable as an atomic operation.

## Return value

The function returns the resulting incremented value.

## Examples

`Result = interlocked_increment(.my_gloabl_counter);`

Increments a global variable by **`1`** and saves the results into a local variable named `Result`.

## **Remarks**

None

## Related

[interlocked_compare_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_compare_exchange)

[interlocked_decrement](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_decrement)

[interlocked_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange)

[interlocked_exchange_add](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange_add)
