---
description: An example of changing system state
---

# change system state (registers, memory, variables)

Changing the system state is a crucial task for the debuggers. You can change the system's state by using different commands in the debugger. It's also possible to change the system state by using the script engine.

In this example, we'll describe general examples that can be used to modify different registers, memory, and variables.

{% hint style="success" %}
You can use all script engine statements in the script section of events or run them using the '[?](https://docs.hyperdbg.org/commands/debugging-commands/eval)' command.
{% endhint %}

### Changing registers

HyperDbg supports more than 100 registers and flags. You can see a complete list of these registers [here](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations). Changing registers is as easy as assigning new values to them.

For example,

If you want to change the **RAX** register to **0x55**.

```clike
? @rax = 0x55;
```

Evaluationg a statement and changing **EBX** register by its result. 

```clike
? @ebx = @rdx | 0xfffff550 + @edx;
```

You can also change the control registers, debug register, etc.

```clike
? @cr3 =  0xff00ee;
```

You can even change the program's flow by modifying the program counter (PC) and stack pointer.

```clike
? @rip =  0xfffff8003ad6f010; @rsp = fffff800`5b660000;
```

### Changing global & local variables

You can change the local and global variables exactly the same as changing registers.

The following statement is used to change a global variable.

```clike
? .my_global_var =  @rax + @edx;
```

Or for the local variable:

```clike
? my_local_var =  @rax + @edx;
```

### Changing memory

Modifying memory is possible through '[memory](https://docs.hyperdbg.org/commands/scripting-language/functions/memory)' functions.

The following code changes a **byte** to 0x90 at the location that the **RCX** register is pointing to, then adds **0x8** to it.

```clike
IsEditApplied = eb(poi(@rcx)+8, 0x90);
```

The following code edits memory (**quad-word**) at `fffff8031d44fde0` and change it to `0x12345678deadbeef`.

```clike
IsEditApplied = eq(fffff8031d44fde0, 0x12345678deadbeef);
```

You can also use these functions without checking for the return value.

```clike
eq(fffff8031d44fde0, 0x12345678deadbeef);
```

### Checking Address Validity

In HyperDbg [check_address](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/check_address) function is used to check an address to see if the address is **valid** and **safe to access** from or not.

You can use this function with an **if** statement.

```c
if (check_address(@r11) == 1) 
{ 
    printf("address is valid.\n");
}
else 
{
    printf("address is invalid.\n");
}
```

Or assign the results to a variable.

```clike
IsValid = check_address(@rcx + 10);
```

###
