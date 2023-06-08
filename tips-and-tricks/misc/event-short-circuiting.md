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

### Manually Changing The Short-circuiting State&#x20;

Using the '[events](https://docs.hyperdbg.org/commands/debugging-commands/events)' command, you can change the short-circuiting state of the event for the **corresponding** execution.&#x20;

For example, the following event is triggered and paused by the debugger.

```clike
!syscall 0x55 pid 1c0
```

Now, you conclude that the execution of SYSCALL for the current event (only for the current execution) should be ignored. Thus, you can use the following command to short-circuit the event.

```
0: kHyperDbg> events sc on
```

{% hint style="info" %}
Please note that the default short-circuiting state of the above event is '**off**' which means that the next execution of the event will **execute** the **syscall**. In other words, short-circuiting by using the '[events](https://docs.hyperdbg.org/commands/debugging-commands/events)' command will ignore the execution (emulation) of the event only for **ONE** time.
{% endhint %}

### Using Scripts To Change The Short-circuiting State

The mostly used scenario is applying conditions and ignoring events in certain conditions by using the scripts. This can be done by using the '[event\_sc](https://docs.hyperdbg.org/commands/scripting-language/functions/events/event\_sc)' function.

This function gets `1` or `0` as the input while `1` means the short-circuiting is ON and the event should be ignored and `0` means the event is not short-circuited and should be executed (emulated).

Same as the '[events](https://docs.hyperdbg.org/commands/debugging-commands/events)' command, this function only applies to the current execution (one-time) of the event.

Using this function you can have two approaches: **Blocklisting** and **whitelisting**.&#x20;

#### Blocklisting Approach

In the blocklisting approach, you only short-circuit certain events that met a special condition while the default short-circuiting state is **off**.

For example,

```clike
!syscall script {

	if (@rcx == 0xf000 && @rdx == 0x55) {
		event_sc(1);
	}
}
```

#### Whitelisting Approach

In the whitelisting approach, you allow certain events that met a special condition while the default short-circuiting state is **on** and all other events will be short-circuited.

For example,

```clike
!syscall 0x55 sc on script {
	
	if (@rcx == 0xf0) {
		event_sc(0);
	}
}
```

### Examples

Event short-circuiting can be applied in numerous scenarios.

#### Example 1

The first scenario is for preventing memory modifications. For this example, assume that we compiled the following code.

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

We run the above code, we'll see the following console results:

```
Address of test variable: 7ff71f118210 | pid: 3fa8
test value is : 1
test value is : 2
test value is : 3
test value is : 4
...
```

Now, let's return to HyperDbg. Based on the above output, we use the following script to prevent memory writing (`test++;`) on the test variable's memory.

```clike
!monitor w 7ff71f118210 7ff71f118210+4 pid 3fa8 script {

  printf("writng into memory address: %llx is ignored\n", $context);
  event_sc(1);
}
```

You can see that the target process starts to produce results with the same number in the `test` variable.

```
Address of test variable: 7ff71f118210 | pid: 3fa8
test value is : 1
test value is : 2
test value is : 3
test value is : 4
test value is : 4
test value is : 4
test value is : 4
test value is : 4
...
```

If we remove the events using the following command, the process will get a chance to execute the target instructions to modify the memory (increase the value).

```
0: kHyperDbg> events c all
```

The result will be:

```
Address of test variable: 7ff71f118210 | pid: 3fa8
test value is : 1
test value is : 2
test value is : 3
test value is : 4
test value is : 4
test value is : 4
test value is : 4
test value is : 4
test value is : 5
test value is : 6
test value is : 7
...
```

That's how you can prevent the modification of memory and it's clear that it's possible to use conditional statements to only prevent the execution in certain conditions.

#### Example 2

The second example is preventing a special MSR (`MSR_LSTAR`) from reading its original value. For example, assume the original value is changed and the SYSCALL handler points to another location, then we can use the following script to change the address when a kernel-level module tries to read this MSR, and will see the modified version.

```
!msrread c0000082 script {
	
	//
	// Fill the EDX:EAX
	//
	@rdx = f0f0f0f0;
	@rax = 10203040;
	
	printf("msr %llx read!\n", $context);
	event_sc(1);
}
```

#### Example 3

The third example is event short-circuiting system-calls.

In this example, we decode the details of the system-call related to sending network (socket) packets to the internet and blocked the connection to HTTPS (port: **443**). For more information, please read the detail provided [here](https://www.cyberus-technology.de/posts/network-analysis-with-tycho/).

```clike
!syscall script {

  if (@rax == 0x7) {
  
    if (dw(@rsp + 30) == 0x12007) {

      //
      // Get the port address
      //
      port_num_high_bit = db(poi(@rsp + 38) + 1a);
      port_num_low_bit = db(poi(@rsp + 38) + 1b);

      port_num = 0;
      port_num = port_num_high_bit << 8 | port_num_low_bit;

      //
      // Get the IP address
      //
      part0 = db(poi(@rsp + 38) + 1c);
      part1 = db(poi(@rsp + 38) + 1d);
      part2 = db(poi(@rsp + 38) + 1e);
      part3 = db(poi(@rsp + 38) + 1f);

      part0 = part0 << 0n24;
      part1 = part1 << 0n16;
      part2 = part2 << 0n8;
      part3 = part3 << 0n0;

      ip_addr = part0 | part1 | part2 | part3;

      printf("Process Id: %x, name: %s connects to ====> Address: %d.%d.%d.%d:%d\n",
		$pid,
		$pname,
        (ip_addr & 0xFF000000) >> 0n24,
        (ip_addr & 0x00FF0000) >> 0n16,
        (ip_addr & 0x0000FF00) >> 0n8,
        ip_addr & 0x000000FF,
		port_num);
		
	if (port_num == 0n443) {
			
		//
		// Block the connection to port 443
		//
		printf("Connection to port 443 is blocked!\n");
		event_sc(1);
	}
    }
  }
}
```
