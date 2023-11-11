---
description: Description of the 'spinlock_lock' function in HyperDbg Scripts
---

# spinlock\_lock

### Function

> spinlock\_lock

### Syntax

> spinlock\_lock( \*Variable );

### Parameters

**\[\*Variable (Lock)]**

A reference to a variable (most of the time a global variable), used as a [lock](https://en.wikipedia.org/wiki/Spinlock).

### Description

Locks the spinlock referred to by lock. The lock can be unlocked by [spinlock\_unlock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock\_unlock).

### Return value

None

### Examples

First of all, you should define a global variable and assign zero to it (unlocked state).

`? .my_lock = 0;`

Then, you can use it like this:

`spinlock_lock(&.my_lock);`

The above example locks the spinlock of a global variable (lock) called `.my_lock`. Note that you should use the unary operator `&` to pass a reference of the global variable to this function.

### Remarks

None

### Related

[spinlock\_lock\_custom\_wait](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock\_lock\_custom\_wait)

[spinlock\_unlock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks/spinlock\_unlock)
