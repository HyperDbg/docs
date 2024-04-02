---
description: Description of the 'interlocked_decrement' function in HyperDbg Scripts
---

# interlocked\_decrement

### Function

> interlocked\_decrement

### Syntax

> interlocked\_decrement( \*Variable );

### Parameters

**\[\*Variable (Addend)]**

A reference to a variable (most of the time a global variable) to be subtracted by `1`.

### Description

Decrements (decreases by one) the value of the specified global variable as an atomic operation.

### Return value

The function returns the resulting decremented value.

### Examples

`Result = interlocked_decrement(.my_gloabl_counter);`

Decrements a global variable by `1` and saves the results into a local variable named `Result`.

### Remarks

None

### Related

[interlocked\_compare\_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked\_compare\_exchange)

[interlocked\_increment](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked\_increment)

[interlocked\_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked\_exchange)

[interlocked\_exchange\_add](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked\_exchange\_add)
