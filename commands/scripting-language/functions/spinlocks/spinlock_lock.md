---
description: Description of 'spinlock_lock' function in HyperDbg Scripts
---

# spinlock\_lock

### Function

> spinlock\_lock

### Syntax

> spinlock\_lock\( GlobalVariable \);

### Parameters

**\[GlobalVariable\]**

         A global variable, used as a [lock](https://en.wikipedia.org/wiki/Spinlock).

### Description

Locks the spinlock referred to by lock. The lock can be unlocked by [spinlock\_unlock](https://docs.hyperdbg.com/commands/scripting-language/functions/spinlocks/spinlock_unlock).

### Return value

None

### Examples

`spinlock_lock(.my_lock_var);`

Locks the spinlock of a global variable \(lock\) called `.my_lock_var`.

### **Remarks**

None

### Related

[spinlock\_lock\_custom\_wait](https://docs.hyperdbg.com/commands/scripting-language/functions/spinlocks/spinlock_lock_custom_wait)

[spinlock\_unlock](https://docs.hyperdbg.com/commands/scripting-language/functions/spinlocks/spinlock_unlock)

