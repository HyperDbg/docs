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

In HyperDbg's script engine, all the variables are defined without **Type**, and all of them are considered unsigned 64-bit integers. You can save results of functions and boolean expressions or results of mathematical calculations alongside 64-bit addresses to the variables.

The variables can be used as an input to other functions or might be used in conditional statements or loops.

If you want to assign a symbol name (e.g., address of a Windows function or a Windows global variable) to a script engine variable, you should specify the module name along with a bang '**!**' character; otherwise, it's interpreted as a variable name. For example, `x = ExAllocatePoolWithTag;` is equal to assigning the a script engine **variable** named _**ExAllocatePoolWithTag**_ to a variable named _**x**_. However, `x = nt!ExAllocatePoolWithTag;` means assigning the **function address** from the **nt** module to the _**x**_ variable.

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

Or, you can decrement or increment variables by one.

```clike
my_variable++;      // equals to my_variable = my_variable + 1;
my_variable--;      // equals to my_variable = my_variable - 1;
```

Local variables won't be changed in the case of a core's context, which means you can save the variable and expect to reread it next time you access the variable from the same core. But of course, the local variables are not available in other cores.

## Global Variables Assignment

Like local variables, all global variables are defined without **type**, and all of them are considered unsigned 64-bit integers.

The variables can be used as an input to other functions or might be used in conditional statements or loops.

You can also use global variables as [volatile](https://en.wikipedia.org/wiki/Volatile_\(computer_programming\)) variables to the [spinlocks](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks) or [interlocked](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked) functions.

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

Or, you can decrement or increment variables by one.

```clike
.my_variable++;      // equals to my_variable = my_variable + 1;
.my_variable--;      // equals to my_variable = my_variable - 1;
```

### Using global variables on multi-core systems

You should not write to a global variable simultaneously from different cores. It's clear that other cores might trigger the same event and use the global variable or modify that variable.

To solve this problem, you can use [spinlock](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks) functions. If you want to perform mathematical calculations on different global variables, you should use [interlocked](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked) functions instead of performing them using regular math operators.

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

As another example, assume that we want to count the number of times a function is called. For this purpose, we need a global variable to hold this number. You can safely use [interlocked\_increment](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_increment) for this purpose, and for other mathematical operations or [atomic](https://wiki.osdev.org/Atomic_operation) exchange operations, you can use other [**interlocked**](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/) functions.

```c
interlocked_increment(.my_global_counter);
```

If you are running HyperDbg on a **single-core** machine, there is no need to use a **spinlock** or use **interlocked** functions for calculations; you can directly modify them without any problem.

{% hint style="info" %}
Both global variables and local variables are initialized with `NULL`.
{% endhint %}

## Compound Assignment

The following compound assignments are supported.

| Operator | Description               | Equivalent Expression    | Meaning                                                                     |
| -------- | ------------------------- | ------------------------ | --------------------------------------------------------------------------- |
| +=       | Addition assignment       | `a += b` → `a = a + b`   | Adds the value of `b` to `a` and stores the result in `a`.                  |
| -=       | Subtraction assignment    | `a -= b` → `a = a - b`   | Subtracts the value of `b` from `a` and stores the result in `a`.           |
| \*=      | Multiplication assignment | `a *= b` → `a = a * b`   | Multiplies `a` by `b` and stores the result in `a`.                         |
| /=       | Division assignment       | `a /= b` → `a = a / b`   | Divides `a` by `b` and stores the quotient in `a`.                          |
| %=       | Modulus assignment        | `a %= b` → `a = a % b`   | Stores the remainder of `a` divided by `b` in `a`.                          |
| <<=      | Left shift assignment     | `a <<= b` → `a = a << b` | Shifts the bits of `a` left by `b` positions and stores the result in `a`.  |
| >>=      | Right shift assignment    | `a >>= b` → `a = a >> b` | Shifts the bits of `a` right by `b` positions and stores the result in `a`. |
| &=       | Bitwise AND assignment    | `a &= b` → `a = a & b`   | Performs a bitwise AND between `a` and `b`, storing the result in `a`.      |
| ^=       | Bitwise XOR assignment    | `a ^= b` → `a = a ^ b`   | Performs a bitwise XOR between `a` and `b`, storing the result in `a`.      |
| \|=      | Bitwise OR assignment     | `a \|= b` → `a = a \| b` | Performs a bitwise OR between `a` and `b`, storing the result in `a`.       |

Here is an example for the compound assignment.

```clike
t1 = 1;
t1 +=5;
	
printf("t1 is: %x\n", t1); // prints 6 (in Hex)
	
t1 <<=5;
printf("t1 is: %d\n", t1); // prints 192 (in Decimal)
```

## Multiple Assignment

Multiple assignments are described as follows.

For local variables:

```clike
t1 = t2 = 85;

printf("t1 equals to: 0x%x\n", t1); // prints 0x85
printf("t2 equals to: 0x%x\n", t2); // prints 0x85
```

Similarly, for global variables:

```clike
.gv1 = .gv2 = .gv3 = 85;

printf("global variable gv1 equals to: 0x%x\n", .gv1); // prints 0x85
printf("global variable gv2 equals to: 0x%x\n", .gv2); // prints 0x85
printf("global variable gv3 equals to: 0x%x\n", .gv3); // prints 0x85
```

## Arrays

HyperDbg also supports arrays within scripts.

### Initializing Array

The following statements are used for initializing arrays.

```clike
int t1 [3] = {0};
```

### Array Assignment

The following example shows how to assign constant values to an array.

```clike
int t1 [6] = {1,2,3,4,5,6};
```

You can also assign variables to an array.

```clike
t44 = 55;
int t1[6] = {1,2,3,t44,5,6};
```

### Reading Array Elements

There are two ways you can read each array item: first, by using its index, or by dereferencing it directly from memory.

By using its index:

```clike
int t1[6] = {1,2,3,4,5,6};

for (i = 0; i < 6 ; i++){
	printf("%d\n", t1[i]);
}
```

By directly reading it from memory:

```clike
int t1[6] = {1,2,3,4,5,6};

for( i = 0; i < 6 ; i++){
	printf("%d\n", *(i+t1));
}
```

### Multidimensional Array

Multidimensional arrays are defined as follows.

```clike
int t1 [3][3] = {{1,2,3},{4,5,6},{7,8,9}};

printf("%d\n",t1[0][0]); // prints 1
printf("%d\n",t1[0][1]); // prints 2
printf("%d\n",t1[0][2]); // prints 3

printf("%d\n",t1[1][0]); // prints 4
printf("%d\n",t1[1][1]); // prints 5
printf("%d\n",t1[1][2]); // prints 6

printf("%d\n",t1[2][0]); // prints 7
printf("%d\n",t1[2][1]); // prints 8
printf("%d\n",t1[2][2]); // prints 9
```

## Modify Memory

Modifying memory is possible using '[eb, ed, eq](https://docs.hyperdbg.org/commands/scripting-language/functions/eb-ed-eq)' functions.

`eb` modifies a single `byte`.

`ed` modifies a `dwrod`.

`eq` modifies a `qword` value.

The following code edits memory (**quad-word**) at `fffff8031d44fde0` and change it to `0x12345678deadbeef`.

```c
IsEditApplied = eq(fffff8031d44fde0, 0x12345678deadbeef);
```

The following code changes a **byte** to 0x90 at the location that the **@rcx** register is pointing to, then adds 0x8 to it.

```c
IsEditApplied = eb(poi(@rcx)+8, 0x90);
```
