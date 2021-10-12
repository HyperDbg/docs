---
description: Description of 'interlocked_compare_exchange' function in HyperDbg Scripts
---

# interlocked_compare_exchange

## Function

> interlocked_compare_exchange

## Syntax

> interlocked_compare_exchange( \*Variable, Expression, Expression );

## Parameters

**\[\*Variable (Destination)]**

A reference to a variable (most of the time a global variable) that is compared with the value of **Comperand**.

**\[Expression (ExChange)]**

Specifies the output value pointed to by **Destination** if the input value pointed to by **Destination** equals the value of **Comperand**.

**\[Expression (Comperand)]**

Specifies the value that is compared with the input value pointed to by **Destination**.

## Description

Performs an atomic operation that compares the input value pointed to by **Destination** with the value of **Comperand**.

## Return value

The function returns the original value of **\*Destination**.

## Examples

None

## **Remarks**

None

## Related

[interlocked_exchange_add](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange_add)

[interlocked_increment](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_increment)

[interlocked_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange)

[interlocked_decrement](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_decrement)
