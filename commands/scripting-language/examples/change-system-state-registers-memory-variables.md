# change system state (registers, memory, variables)

Changing the system state is a crucial task for the debuggers. You can change the system's state by using different commands in the debugger. It's also possible to change the system state by using script engine.

In this example, we'll describe about general examples that can be used to modify different registers, memory, and variables.

{% hint style="success" %}
You can use all of the script engine statements either in script section of events or running them using the '[?](https://docs.hyperdbg.org/commands/debugging-commands/eval)' command.
{% endhint %}

### Changing registers

HyperDbg support more than 100 registers and flags, you can see a complete list of these registers [here](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations). Changing registers is as easy as assigning new values to them.

For example,

If you want to change **RAX** register to **0x55**.

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

You can even change the program's flow by modifying program counter.



