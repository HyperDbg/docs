---
description: Description of 'interlocked_increment' function in HyperDbg Scripts
---

# interlocked\_increment

## Function

> interlocked\_increment

## Syntax

> interlocked\_increment\( GlobalVariable \);

## Parameters

**\[GlobalVariable \(Addend\)\]**

```text
     A global variable to be added by `1`.
```

## Description

Increments \(increases by one\) the value of the specified global variable as an atomic operation.

## Return value

The function returns the resulting incremented value.

## Examples

`Result = interlocked_increment(.my_gloabl_counter);`

Increments a global variable by **`1`** and saves the results into a local variable named `Result`.

## **Remarks**

None

## Related

[interlocked\_compare\_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_compare_exchange)

[interlocked\_decrement](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_decrement)

[interlocked\_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange)

[interlocked\_exchange\_add](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange_add)

