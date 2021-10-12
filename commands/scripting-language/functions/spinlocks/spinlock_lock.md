---
description: Description of 'spinlock_lock' function in HyperDbg Scripts
---

# spinlock_lock

## Function

> spinlock_lock

## Syntax

> spinlock_lock( GlobalVariable );

## Parameters

**\[GlobalVariable (Lock)]**

```
     A global variable, used as a [lock](https://en.wikipedia.org/wiki/Spinlock).
```

## Description

Locks the spinlock referred to by lock. The lock can be unlocked by [spinlock_unlock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_unlock).

## Return value

None

## Examples

`spinlock_lock(&.my_lock_var);`

Locks the spinlock of a global variable (lock) called `.my_lock_var`. Note that you should use unary operator `&` to pass a reference of the global variable to this function.

## **Remarks**

None

## Related

[spinlock_lock_custom_wait](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock_custom_wait)

[spinlock_unlock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_unlock)
