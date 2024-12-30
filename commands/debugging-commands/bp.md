---
description: Description of the 'bp' command in HyperDbg.
---

# bp (set breakpoint)

### Command

> bp

### Syntax

> bp \[Address (hex)] \[pid ProcessId (hex)] \[tid ThreadId (hex)] \[core CoreId (hex)]

### Description

Puts a breakpoint (**0xcc**) on the target function in user-mode and kernel-mode.

{% hint style="danger" %}
In **HyperDbg**, the 'bp' breakpoints are **NOT** [events](https://docs.hyperdbg.org/design/debugger-internals/events). If you want to use breakpoint in an event-like form (e.g., if you want to create logs using script-engine), you should use [!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook) command instead.
{% endhint %}

{% hint style="info" %}
If you use the 'bp' command, **HyperDbg** won't hide your breakpoint for the applications that read the memory. The only reason to use '**bp**' instead of [!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook) is that '**bp**' is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution. However, the in [!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook) the guest will be continued for some time, and you lose the current context.
{% endhint %}

### Parameters

**\[Address (hex)]**

The **Virtual** address of where we want to put a breakpoint.

**\[pid ProcessId (hex)] \[tid ThreadId (hex)] \[core CoreId (hex)] (optional)**

Optional value to trigger breakpoint in just one special process or one special thread, or one special core. Add `pid xx` to your command or `tid yy` or `core zz`; thus, the command will be executed if the process id is equal to `xx` or the thread id is equal to `yy` or the core is equal to `zz` . If you don't specify these options, then by default, you receive breakpoints on all conditions. See the **Remarks** section for more information about **pid**.

### Context

As the **Context**, HyperDbg sends the **virtual** address of where the breakpoint is triggered (`RIP` of the triggered breakpoint).

### Examples

If you want to put breakpoints on `nt!ExAllocatePoolWithTag`, `nt!ExAllocatePoolWithTag+5`,`nt!ExAllocatePoolWithTag+@rax+5`, `fffff801639b1035`, `fffff801639b103a`, and `fffff801639b103f`, you can use the following commands.

```
0: kHyperDbg> bp nt!ExAllocatePoolWithTag
```

```
0: kHyperDbg> bp nt!ExAllocatePoolWithTag+5
```

```
0: kHyperDbg> bp nt!ExAllocatePoolWithTag+@rax+5
```

```
0: kHyperDbg> bp fffff801`639b1035
```

```
0: kHyperDbg> bp fffff801`639b103a
```

```
0: kHyperDbg> bp fffff801`639b103f
```

After that, you can see a list of active breakpoints using the '[bl](https://docs.hyperdbg.org/commands/debugging-commands/bl)' command.

```
HyperDbg> bl
id   address           status
--   ---------------   --------
01   fffff801639b1030  enabled
02   fffff801639b1035  enabled
03   fffff801639b1040  enabled
04   fffff801639b1035  enabled
05   fffff801639b103a  enabled
06  fffff801639b103f  enabled
```

### SDK

To set the breakpoint, you need to use the following function in `libhyperdbg`:

```clike
VOID
hyperdbg_u_set_breakpoint(UINT64 address, UINT32 pid, UINT32 tid, UINT32 core_numer);
```

### Remarks

In this command, `pid xx` does not mean that we will change the layout to a new process, it means that the address should be available in the current process layout but will be triggered only on the process with process id equal to `xx`, you can use the '[.process](https://docs.hyperdbg.org/commands/meta-commands/.process)' command to switch to a new process if you want to put a breakpoint on the layout of another process.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[!epthook (hidden hook with EPT - stealth breakpoints)](https://docs.hyperdbg.org/commands/extension-commands/epthook)

[bl (list breakpoints)](https://docs.hyperdbg.org/commands/debugging-commands/bl)

[be (enable breakpoints)](https://docs.hyperdbg.org/commands/debugging-commands/be)

[bd (disable breakpoints)](https://docs.hyperdbg.org/commands/debugging-commands/bd)

[bc (clear and remove breakpoints)](https://docs.hyperdbg.org/commands/debugging-commands/bc)
