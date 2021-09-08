---
description: Description of 'interlocked_decrement' function in HyperDbg Scripts
---

# interlocked\_decrement

### Function

> interlocked\_decrement

### Syntax

> interlocked\_decrement\( GlobalVariable \);

### Parameters

**\[GlobalVariable \(Addend\)\]**

         A global variable to be subtracted by `1`.

### Description

Decrements \(decreases by one\) the value of the specified global variable as an atomic operation.

### Return value

The function returns the resulting decremented value.

### Examples

`Result = interlocked_decrement(.my_gloabl_counter);`

Decrements a global variable by **`1`** and saves the results into a local variable named `Result`.

### **Remarks**

None

### Related

[interlocked\_compare\_exchange](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_compare_exchange)

[interlocked\_increment](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_increment)

[interlocked\_exchange](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_exchange)

[interlocked\_exchange\_add](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_exchange_add)

