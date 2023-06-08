---
description: The event short-circuiting and ignoring mechanism in HyperDbg
---

# Event short-circuiting

Starting from **version 0.3**, HyperDbg introduces event short-circuiting as a mechanism, allowing the ignoring of certain events. For example, imagine that a SYSCALL event is triggered in the debuggee, now based on the user-defined conditions, you want to ignore the execution of this event as if no SYSCALL instruction is executed. This is where event short-circuiting becomes handy.&#x20;

The same scenario happens to most of the errors, for example, certain IN/OUT instructions need to be ignored. Again this mechanism can be used. Or for instance, you want to ignore the execution of an RDMSR or a WRMSR, again event short-circuiting can be used.

Event short-circuiting is also handy in the '[!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)' command. Imagine, you want to ignore memory writes to the certain field of a structure. You can simply ignore the memory write and write something else in the target location.

This mechanism is mainly handled by using two commands and a function.

### Default Short-circuiting State

The default short-circuiting state is used whenever you want to short-circuit all of the executions of the target event. For example, one might want to block the execution of IN/OUT instructions to port **0x3F8**. In this case, we can add `sc on` to the target event.

```clike
!ioin 0x3f8 sc on script { printf("IO port (in) has been access.\n"); }
```

for the **OUT** instructions.

```clike
!ioout 0x3f8 sc on script { printf("IO port (out) has been access.\n"); }
```

The same 'sc on' can also be applied to other events like [!syscall](https://docs.hyperdbg.org/commands/extension-commands/syscall), [!msrread](https://docs.hyperdbg.org/commands/extension-commands/msrread), [!msrwrite](https://docs.hyperdbg.org/commands/extension-commands/msrwrite), [!monitor](https://docs.hyperdbg.org/commands/extension-commands/cpuid), etc.  Certain events like [!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook) and [!epthook2](https://docs.hyperdbg.org/commands/extension-commands/epthook2) don't support event short-circuiting as this concept doesn't make sense in the case of these events. Please check the documentation of each event to see whether the target event supports this mechanism or not.

By default, the '**sc**' state of the event is set to **off**, which means the event will be triggered while the target instruction also will be executed.

### Examples

There are plenty of scenarios

```clike
#include <iostream>
#include <Windows.h>

volatile int test = 0;

int main()
{
	printf("Address of test variable: %llx | pid: %x\n", &test, GetCurrentProcessId());

	for (;;)
	{
		test++;
		printf("test value is : %d\n", test);
		Sleep(2000);
	}
}
```
