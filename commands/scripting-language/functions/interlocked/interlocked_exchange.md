---
description: Description of 'interlocked_exchange' function in HyperDbg Scripts
---

# interlocked\_exchange

### Function

> interlocked\_exchange

### Syntax

> interlocked\_exchange\( GlobalVariable, Expression \);

### Parameters

**\[GlobalVariable \(Target\)\]**

         A global variable to be exchanged. The function sets this variable to **Value** and returns its prior value.

**\[Expression \(Value\)\]**

         The value to be exchanged with the value pointed to by **Target**.

### Description

Sets a variable to the specified value as an atomic operation.

### Return value

Returns the initial value of the **Target** parameter.

### Examples

None

### **Remarks**

None

### Related

[interlocked\_compare\_exchange](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_compare_exchange)

[interlocked\_increment](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_increment)

[interlocked\_exchange\_add](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_exchange_add)

[interlocked\_decrement](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_decrement)

