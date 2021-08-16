---
description: Description of user-defined functions and assignments
---

# Functions & Assignments

## Register Assignment

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

## Variable Assignment

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

## Modify Memory





