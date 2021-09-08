---
description: Description of 'interlocked_exchange_add' function in HyperDbg Scripts
---

# interlocked\_exchange\_add

### Function

> interlocked\_exchange\_add

### Syntax

> interlocked\_exchange\_add\( GlobalVariable, Expression \);

### Parameters

**\[GlobalVariable\]**

         A global variable to add the value of the resulting instruction to it.

**\[Expression\]**

         The value which is added to the global variable.

### Description

Performs an atomic addition of two values.

### Return value

The function returns the initial value of the **`GlobalVariable`** parameter

### Examples

`Result = interlocked_exchange_add(.my_gloabl_counter, 0x55);`

Adds the value \(`0x55`\) to the global variable and saves the initial value of the `.my_gloabl_counter` into a local variable named `Result`.

### **Remarks**

You can also perform the subtraction by addition \(using the 2's-complement system\).

### Related

[interlocked\_compare\_exchange](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_compare_exchange)

[interlocked\_increment](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_increment)

[interlocked\_exchange](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_exchange)

[interlocked\_decrement](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_decrement)

