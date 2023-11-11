---
description: Description of the 'interlocked_exchange' function in HyperDbg Scripts
---

# interlocked\_exchange

### Function

> interlocked\_exchange

### Syntax

> interlocked\_exchange( \*Variable, Expression );

### Parameters

**\[\*Variable (Target)]**

A reference to a variable (most of the time a global variable) to be exchanged. The function sets this variable to **Value** and returns its prior value.

**\[Expression (Value)]**

The value to be exchanged with the value pointed to by **Target**.

### Description

Sets a variable to the specified value as an atomic operation.

### Return value

Returns the initial value of the **Target** parameter.

### Examples

None

### Remarks

None

### Related

[interlocked\_compare\_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked\_compare\_exchange)

[interlocked\_increment](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked\_increment)

[interlocked\_exchange\_add](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked\_exchange\_add)

[interlocked\_decrement](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked\_decrement)
