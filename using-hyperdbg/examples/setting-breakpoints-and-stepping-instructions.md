---
description: 'Set breakpoint, Step-over, and Step-in'
---

# Setting Breakpoints & Stepping Instructions

In HyperDbg, we have multiple options to set a breakpoint.

One of the ways of setting breakpoints is [hooking](https://docs.hyperdbg.com/using-hyperdbg/examples/hooking-any-function). Another way is using the ['bp' command](https://docs.hyperdbg.com/commands/debugging-commands/bp). In this article, we describe the second method.

Assume that `ObRegisterCallbacks` is located at ``fffff805`5cbac610``.

This function creates callbacks for thread, process, and other objects' [tasks ](https://rayanfam.com/topics/reversing-windows-internals-part1/)like creation, opening, etc. You can see more information at [MSDN](https://docs.microsoft.com/en-us/windows-hardware/drivers/ddi/wdm/nf-wdm-obregistercallbacks).

Many game anti-cheat solutions use this function to monitor processes to prevent game cheater to cheat on games.

In order to bypass this mechanism, we use the following command in HyperDbg to set a breakpoint on this function.

```text
HyperDbg> bp fffff805`5cbac610
```

Then, we will run our game and see if the breakpoint is triggered or not.

If the breakpoint is triggered, then the system is halt and we are able to control the debuggee.

After that, we can use the ['p' command](https://docs.hyperdbg.com/commands/debugging-commands/p) to step-over the instructions.

```text
HyperDbg> p 
fffff805`5cbac610    48 81 EC 50 01 00 00                sub rsp, 0x150
```

You can also add a number to instrument the instructions multiple times.

```text
HyperDbg> p 3
fffff805`5cbac610    48 81 EC 50 01 00 00                sub rsp, 0x150
fffff805`5cbac617    48 8D AC 24 80 00 00 00             lea rbp, ss:[rsp+0x80]
fffff805`5cbac620    C6 45 AB 00                         mov byte ptr ss:[rbp-0x55], 0x00
```

If you want to step-in, you can use the ['t' command](https://docs.hyperdbg.com/commands/debugging-commands/t).

```text
HyperDbg> t
fffff805`5cbac610    48 81 EC 50 01 00 00                sub rsp, 0x150
```

