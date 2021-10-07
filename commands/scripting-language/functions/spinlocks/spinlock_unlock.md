---
description: Description of 'spinlock_unlock' function in HyperDbg Scripts
---

# spinlock\_unlock

## Function

> spinlock\_unlock

## Syntax

> spinlock\_unlock\( GlobalVariable \);

## Parameters

**\[GlobalVariable \(Lock\)\]**

```text
     A global variable, used as a [lock](https://en.wikipedia.org/wiki/Spinlock). The lock can be locked by [spinlock\_lock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock).
```

## Description

Unlocks the spinlock referred to by lock.

## Return value

None

## Examples

`spinlock_unlock(.my_lock_var);`

unlocks the spinlock of a global variable \(lock\) called `.my_lock_var`.

## **Remarks**

None

## Related

[spinlock\_lock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock)

[spinlock\_lock\_custom\_wait](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock_lock_custom_wait)

