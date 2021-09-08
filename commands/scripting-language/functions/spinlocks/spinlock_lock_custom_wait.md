---
description: Description of 'spinlock_lock_custom_wait' function in HyperDbg Scripts
---

# spinlock\_lock\_custom\_wait

### Function

> spinlock\_lock\_custom\_wait

### Syntax

> spinlock\_lock\_custom\_wait\( GlobalVariable, Expression \);

### Parameters

**\[GlobalVariable\]**

         A global variable, used as a [lock](https://en.wikipedia.org/wiki/Spinlock).

**\[Expression\]**

         A [MASM-like expression](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the maximum wait time for the spinlock to re-check the lock.

{% hint style="info" %}
By default in [spinlock\_lock](https://docs.hyperdbg.com/commands/scripting-language/functions/spinlocks/spinlock_lock), the maximum waiting time is **`65536`**.
{% endhint %}

### Description

Locks the spinlock referred to by lock and also uses the maximum wait time to re-check the lock. The lock can be unlocked by [spinlock\_unlock](https://docs.hyperdbg.com/commands/scripting-language/functions/spinlocks/spinlock_unlock).

### Return value

None

### Examples

`spinlock_lock_custom_wait(.my_lock_var, 500);`

Locks the spinlock of a global variable \(lock\) called `.my_lock_var`, it used **500** as the maximum wait time.

### **Remarks**

If your lock needs to be checked more frequently, you should specify a lower value as the second argument. However, it's not a power-efficient approach, but it gives you a more real-time spinlock. If you give a number higher than 65536, it takes longer to re-check the lock by the processor.

It's actually the number of times which the **PAUSE** instruction is executed.

### Related

[spinlock\_lock](https://docs.hyperdbg.com/commands/scripting-language/functions/spinlocks/spinlock_lock)

[spinlock\_unlock](https://docs.hyperdbg.com/commands/scripting-language/functions/spinlocks/spinlock_unlock)

