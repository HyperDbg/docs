---
description: Description of 'interlocked_compare_exchange' function in HyperDbg Scripts
---

# interlocked\_compare\_exchange

### Function

> interlocked\_compare\_exchange

### Syntax

> interlocked\_compare\_exchange\( GlobalVariable, Expression, Expression \);

### Parameters

**\[GlobalVariable \(Destination\)\]**

         A global variable that is compared with the value of **Comperand**.

**\[Expression \(ExChange\)\]**

         Specifies the output value pointed to by **Destination** if the input value pointed to by **Destination** equals the value of **Comperand**.

**\[Expression \(Comperand\)\]**

        Specifies the value that is compared with the input value pointed to by **Destination**.

### Description

Performs an atomic operation that compares the input value pointed to by **Destination** with the value of **Comperand**.

### Return value

The function returns the original value of **\*Destination**.

### Examples

None

### **Remarks**

None

### Related

[interlocked\_exchange\_add](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange_add)

[interlocked\_increment](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_increment)

[interlocked\_exchange](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_exchange)

[interlocked\_decrement](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_decrement)

