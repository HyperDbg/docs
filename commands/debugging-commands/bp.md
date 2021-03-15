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
In **HyperDbg**, the 'bp' breakpoints are **NOT** [events](https://docs.hyperdbg.com/design/debugger-internals/events). If you want to use breakpoint in an event-like form \(e.g., if you want to create logs using script-engine\), you should use [!epthook](https://docs.hyperdbg.com/commands/extension-commands/epthook) command instead.
{% endhint %}

{% hint style="info" %}
If you use the 'bp' command, **HyperDbg** won't hide your breakpoint for the applications that read the memory. The only reason to use '**bp**' instead of [!epthook](https://docs.hyperdbg.com/commands/extension-commands/epthook) is that '**bp**' is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution. However, the in [!epthook](https://docs.hyperdbg.com/commands/extension-commands/epthook) the guest will be continued for some time, and you lose the current context.
{% endhint %}

### Parameters

**\[address\]**

          The **Virtual** address of where we want to put a breakpoint.

**\[pid \| tid \| core \(hex value\)\]** \(optional\)

          Optional value to trigger breakpoint in just one special process or one special thread, or one special core. Add `pid xx` to your command or `tid yy` or `core zz`; thus, the command will be executed if the process id is equal to `xx` or the thread id is equal to `yy` or the core is equal to `zz` . If you don't specify these options, then by default, you receive breakpoints on all conditions. See the **Remarks** section for more information about **pid**.

### Context

As the **Context**, HyperDbg sends the **virtual** address of where the breakpoint is triggered \(`RIP` of the triggered breakpoint\).

### Examples

If you want to put breakpoints on `fffff801639b1030`, `fffff801639b1035`, `fffff801639b103a`, and `fffff801639b103f`, you can use the following commands.

```text
HyperDbg> bp fffff801`639b1030
```

```text
HyperDbg> bp fffff801`639b1035
```

```text
HyperDbg> bp fffff801`639b103a
```

```text
HyperDbg> bp fffff801`639b103f
```

After that, you can see a list of active breakpoints using the '[bl](https://docs.hyperdbg.com/commands/debugging-commands/bl)' command.

```text
HyperDbg> bl
id   address           status
--   ---------------   --------
01   fffff801639b1030  enabled
02   fffff801639b1035  enabled
03   fffff801639b103a  enabled
04   fffff801639b103f  enabled
```

### IOCTL

This commands works over serial by sending the serial packets to the remote computer.

First of all, you should fill the following structure, set the `Address` to your target virtual address that you want to put a breakpoint on it, and fill `Pid` to your special process id, and/or `Tid` to your special thread id, and/or `Core` to your special core.

```c
typedef struct _DEBUGGEE_BP_PACKET {

  UINT64 Address;
  UINT32 Pid;
  UINT32 Tid;
  UINT32 Core;
  UINT32 Result;

} DEBUGGEE_BP_PACKET, *PDEBUGGEE_BP_PACKET;
```

If you want your breakpoint to be triggered for all processes, threads, and cores, then choose `DEBUGGEE_BP_APPLY_TO_ALL_PROCESSES`, `DEBUGGEE_BP_APPLY_TO_ALL_THREADS`, `DEBUGGEE_BP_APPLY_TO_ALL_CORES`.

The next step is sending the above structure to the debuggee when debuggee is paused and waiting for new command on **vmx-root** mode.

You should send the above structure with `DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_ON_VMX_ROOT_BP` as `RequestedAction` and `DEBUGGER_REMOTE_PACKET_TYPE_DEBUGGER_TO_DEBUGGEE_EXECUTE_ON_VMX_ROOT` as `PacketType`.

In return, the debuggee sends the above structure with the following type.

```c
DEBUGGER_REMOTE_PACKET_REQUESTED_ACTION_DEBUGGEE_RESULT_OF_BP
```

In the returned structure, the `Result` is filled by the kernel.

If the `Result` is `DEBUGEER_OPERATION_WAS_SUCCESSFULL`, then the operation was successful. Otherwise, the returned result is an error.

The following function is responsible for sending breakpoint buffers in the debugger.

```c
BOOLEAN KdSendBpPacketToDebuggee(PDEBUGGEE_BP_PACKET BpPacket);
```

### **Remarks**

In this command, **`pid xx`** does not mean that we will change the layout to a new process, it means that the address should be available in the current process layout but will be triggered only on the process with process id equal to **`xx`**, you can use the '[.process](https://docs.hyperdbg.com/commands/meta-commands/.process)' command to switch to a new process if you want to put a breakpoint on the layout of another process. 

This command is guaranteed to keep debuggee in a halt state \(in Debugger Mode\); thus, nothing will change during its execution.

### Requirements

None

### Related

[!epthook \(hidden hook with EPT - stealth breakpoints\)](https://docs.hyperdbg.com/commands/extension-commands/epthook)

[bl \(list breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bl)

[be \(enable breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/be)

[bd \(disable breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bd)

[bc \(clear and remove breakpoints\)](https://docs.hyperdbg.com/commands/debugging-commands/bc)

