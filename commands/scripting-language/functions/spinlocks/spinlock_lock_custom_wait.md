---
description: Description of 'spinlock_lock_custom_wait' function in HyperDbg Scripts
---

# spinlock_lock_custom_wait

## Function

> spinlock_lock_custom_wait

## Syntax

> spinlock_lock_custom_wait( GlobalVariable, Expression );

## Parameters

**\[GlobalVariable (Lock)]**

```
     A global variable, used as a [lock](https://en.wikipedia.org/wiki/Spinlock).
```

**\[Expression]**

```
     A [MASM-like expression](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations) to evaluate which is the maximum wait time for the spinlock to re-check the lock.
```

{% hint style="info" %}
By default in [spinlock_lock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock), the maximum waiting time is **`65536`**.
{% endhint %}

## Description

Locks the spinlock referred to by lock and also uses the maximum wait time to re-check the lock. The lock can be unlocked by [spinlock_unlock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_unlock).

## Return value

None

## Examples

First of all, you should define a global variable and assign zero to it (unlocked state).

`? .my_lock = 0;`

Then, you can use it like this:

`spinlock_lock_custom_wait(&.my_lock, 500);`

The above example locks the spinlock of a global variable (lock) called `.my_lock_var`. Note that you should use the unary operator `&` to pass a reference of the global variable to this function.

Locks the spinlock of a global variable (lock) called `.my_lock_var`, it used **500** as the maximum wait time.

## **Remarks**

If your lock needs to be checked more frequently, you should specify a lower value as the second argument. However, it's not a power-efficient approach, but it gives you a more real-time spinlock. If you give a number higher than 65536, it takes longer to re-check the lock by the processor.

It's actually the number of times which the **PAUSE** instruction is executed.

## Related

[spinlock_lock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock)

[spinlock_unlock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_unlock)
