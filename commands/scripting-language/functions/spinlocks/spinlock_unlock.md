---
description: Description of 'spinlock_unlock' function in HyperDbg Scripts
---

# spinlock_unlock

## Function

> spinlock_unlock

## Syntax

> spinlock_unlock( GlobalVariable );

## Parameters

**\[GlobalVariable (Lock)]**

```
     A global variable, used as a [lock](https://en.wikipedia.org/wiki/Spinlock). The lock can be locked by [spinlock\_lock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock).
```

## Description

Unlocks the spinlock referred to by lock.

## Return value

None

## Examples

`spinlock_unlock(&.my_lock_var);`

unlocks the spinlock of a global variable (lock) which is called `.my_lock`.

## **Remarks**

None

## Related

[spinlock_lock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock)

[spinlock_lock_custom_wait](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock_custom_wait)
