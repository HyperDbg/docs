---
description: Description of user-defined functions and assignments
---

# Functions & Assignments

### Register Assignment

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

### Modify Memory



