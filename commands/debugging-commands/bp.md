---
description: Description of 'bp' command in HyperDbg.
---

# bp \(set breakpoint\)

### Command

> bp

### Syntax

> bp \[address\] \[ pid \| tid \| core \(hex value\)\]

### Description

Puts a breakpoint \(**0xcc**\) on the target function in user-mode and kernel-mode.

{% hint style="danger" %}
In **HyperDbg**, the 'bp' breakpoints are not [events](https://docs.hyperdbg.com/design/debugger-internals/events). If you want to use breakpoint in an event-like form \(e.g., if you want to create logs using script-engine\), you should use [!epthook](https://docs.hyperdbg.com/commands/extension-commands/epthook) command instead.
{% endhint %}

{% hint style="info" %}
If you use the 'bp' command, **HyperDbg** won't hide your breakpoint for the applications that read the memory. The only reason to use '**bp**' instead of [!epthook](https://docs.hyperdbg.com/commands/extension-commands/epthook) is that '**bp**' is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution. However, the in [!epthook](https://docs.hyperdbg.com/commands/extension-commands/epthook) the guest will be continued for some times and you lose the current context.
{% endhint %}

### Parameters

**\[address\]**

          The **Virtual** address of where we want to put a breakpoint.

**\[pid \| tid \| core \(hex value\)\]** \(optional\)

          Optional value to trigger breakpoint in just one special process or one special thread or one special core. Add `pid xx` to your command or `tid yy` or `core zz`; thus, the command will be executed if the process id is equal to `xx` or thread id is equal to `yy` or core is equal to `zz` . If you don't specify these options, then by default, you receive breakpoints on all conditions.

### Context

As the **Context**, **HyperDbg** sends the **virtual** address of where the breakpoint is triggered.

### IOCTL

_Todo_

### **Remarks**

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

[!epthook \(hidden hook with EPT - stealth breakpoints\)](https://docs.hyperdbg.com/commands/extension-commands/epthook)

[bl \(list breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bl)

[be \(enable breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/be)

[bd \(disable breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bd)

[bc \(clear and remove breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bc)

