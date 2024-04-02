---
description: Description of the 'spinlock_lock_custom_wait' function in HyperDbg Scripts
---

# spinlock\_lock\_custom\_wait

### Function

> spinlock\_lock\_custom\_wait

### Syntax

> spinlock\_lock\_custom\_wait( \*Variable, Expression );

### Parameters

**\[\*Variable (Lock)]**

A reference to a variable (most of the time a global variable), used as a [lock](https://en.wikipedia.org/wiki/Spinlock).

**\[Expression]**

A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the maximum waiting check for the spinlock to re-check the lock.

{% hint style="info" %}
By default in [spinlock\_lock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock\_lock), the maximum waiting check is **`65536`**.
{% endhint %}

### Description

Locks the spinlock referred to by lock and also uses the maximum waiting check to re-check the lock. The lock can be unlocked by [spinlock\_unlock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock\_unlock).

### Return value

None

### Examples

First of all, you should define a global variable and assign zero to it (unlocked state).

`? .my_lock = 0;`

Then, you can use it like this:

`spinlock_lock_custom_wait(&.my_lock, 500);`

The above example locks the spinlock of a global variable (lock) which is called `.my_lock`. It uses **500** as the maximum waiting check. Note that you should use the unary operator `&` to pass a reference of the global variable to this function.

### Remarks

If your lock needs to be checked more frequently, you should specify a lower value as the second argument. However, it's not a power-efficient approach, but it gives you a more real-time spinlock. If you give a number higher than 65536, it takes longer to re-check the lock by the processor.

It's actually the number of times which the **PAUSE** instruction is executed.

### Related

[spinlock\_lock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock\_lock)

[spinlock\_unlock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock\_unlock)
