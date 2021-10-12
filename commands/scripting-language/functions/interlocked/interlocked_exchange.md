---
description: Description of 'interlocked_exchange' function in HyperDbg Scripts
---

# interlocked_exchange

## Function

> interlocked_exchange

## Syntax

> interlocked_exchange( \*Variable, Expression );

## Parameters

**\[\*Variable (Target)]**

```
     A reference to a variable (most of the time a global variable) to be exchanged. The function sets this variable to **Value** and returns its prior value.
```

**\[Expression (Value)]**

```
     The value to be exchanged with the value pointed to by **Target**.
```

## Description

Sets a variable to the specified value as an atomic operation.

## Return value

Returns the initial value of the **Target** parameter.

## Examples

None

## **Remarks**

None

## Related

[interlocked_compare_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_compare_exchange)

[interlocked_increment](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_increment)

[interlocked_exchange_add](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange_add)

[interlocked_decrement](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_decrement)
