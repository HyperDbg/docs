---
description: Description of variables and assignments
---

# Variables & Assignments

## Registers Assignment

By using a simple **lvalue** register assignment, the user is able to change the value of registers.

**syntax**

```c
@register = expression;
```

**Example 1**

```c
@rax = 0x55;
```

**Example 2**

```c
@r15 = poi(@rcx);
```

**Example 3**

```c
if ( @rcx == 0x55) {
    @rcx = @rdx + @rax + 12;
}
```

## Local Variables Assignment

In HyperDbg's script engine, all the variables are defined without **Type**, ****and all of them are considered unsigned 64-bit integers. You can save results of functions and boolean expressions or results of mathematical calculations alongside 64-bit addresses to the variables.

The variables can be used as an input to other functions or might be used in conditional statements or loops.

The following example shows the assigning `0` to a variable named `my_variable`.

```c
my_variable = 0;
```

You can also assign registers or pseudo-registers to the variables.

```c
my_variable = $proc + 0x10;
```

```c
my_variable = @rax - @rcx + 8;
```

Also, you can assign the results of functions to the variables.

```c
my_variable = check_address(@rcx);
```

## Global Variables Assignment

Like local variables, all of the global variables are defined without **type**, and all of them are considered unsigned 64-bit integers.

The variables can be used as an input to other functions or might be used in conditional statements or loops.

You can also use global variables as volatile variables to the [spinlocks](https://docs.hyperdbg.com/commands/scripting-language/functions/spinlocks) or [interlocked](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked) functions. 

The difference between local variables and global variables is that the global variables start with a `.` DOT.

The following example shows the assigning `0` to a global variable named `.my_variable`.

```c
.my_variable = 0;
```

You can also assign registers or pseudo-registers to the global variables.

```c
.my_variable = $proc + 0x10;
```

```c
.my_variable = @rax - @rcx + 8;
```

Also, you can assign the results of functions to the global variables.

```c
.my_variable = check_address(@rcx);
```

### Using global variables on multi-core systems

You should not write to a global variable simultaneously from different cores. It's clear that other cores might trigger the same event and use the global variable or modify that variable.

To solve this problem, you can use [spinlock](https://docs.hyperdbg.com/commands/scripting-language/functions/spinlocks) functions and in this case, if you want to perform mathematical calculations on different global variables, you should use [interlocked](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked) functions instead of performing them using regular math operators.

```c
//
// First, we should create a separate global variable as the lock
//
spinlock_lock(.my_global_variable_lock);

//
// Now, it's safe to change the global variable in a multi-core
// environment
//
.my_global_var = 0x1234;


//
// At last, we should release the lock
//
spinlock_unlock(.my_global_variable_lock);
```

As another example, assume that you want to count the number of times that a function is called, for this purpose we need a global variable to hold this number. You can safely use [interlocked\_increment](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/interlocked_increment) for this purpose and for other mathematical operations or [atomic](https://wiki.osdev.org/Atomic_operation) exchange operations, you can use other [**interlocked**](https://docs.hyperdbg.com/commands/scripting-language/functions/interlocked/) functions.

```c
interlocked_increment(.my_global_counter);
```

If you are running HyperDbg on a single-core machine, there is no need to use a **spinlock** or use **interlocked** functions for calculations, you can directly modify them without any problem.

{% hint style="info" %}
Both of the global variables and the local variables are initialized with `NULL`.
{% endhint %}

## Modify Memory

Modifying memory is possible using '[eb, ed, eq](https://docs.hyperdbg.com/commands/scripting-language/functions/eb-ed-eq)' functions.

`eb` modifies a single `byte`.

`ed` modifies a `dwrod`.

`eq` modifies a `qword` value.

The following code edits memory \(**quad-word**\) at `fffff8031d44fde0` and change it to `0x12345678deadbeef`.

```c
IsEditApplied = eq(fffff8031d44fde0, 0x12345678deadbeef);
```

The following code changes a **byte** to 0x90 at the location that the **@rcx** register is pointing to, then adds **0x8** to it.

```c
IsEditApplied = eb(poi(@rcx)+8, 0x90);
```

