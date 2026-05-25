---
description: Description of '!lbr' command in HyperDbg.
---

# !lbr (tracing branches using Last Branch Record)

### Command

> !lbr

### Syntax

> !lbr \[Function (string)]
>
> !lbr \[filter FilterOptions (string)]

### Description

Performs operations for Last Branch Record (LBR), including enabling, disabling, flushing, and configuring filters.

{% hint style="info" %}
To dump the LBR entries after enabling them, use the '[!lbrdump](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)' command.
{% endhint %}

### Parameters

**\[Function (string)]**

The target functionality. Can be one of the following values:

| Function | Description                          |
| -------- | ------------------------------------ |
| enable   | Enable the Last Branch Record        |
| disable  | Disable the Last Branch Record       |
| flush    | Flush (clear) the Last Branch Record |
| filter   | Configure the LBR filter options     |

**\[FilterOptions (string)] (optional)**

One or more filter options to configure which branch types are **not** captured. If no option is specified, everything is captured (default). Can be a combination of the following values:

| Option          | Description                                                                                                                                                                                                                                       |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| kernel          | Do not capture at ring 0                                                                                                                                                                                                                          |
| user            | Do not capture at ring > 0                                                                                                                                                                                                                        |
| jcc             | Do not capture conditional branches                                                                                                                                                                                                               |
| rel\_call       | Do not capture relative calls                                                                                                                                                                                                                     |
| ind\_call       | Do not capture indirect calls                                                                                                                                                                                                                     |
| return          | Do not capture near returns                                                                                                                                                                                                                       |
| ind\_jmp        | Do not capture indirect jumps                                                                                                                                                                                                                     |
| rel\_jmp        | Do not capture relative jumps                                                                                                                                                                                                                     |
| far             | Do not capture far branches (only in legacy LBR)                                                                                                                                                                                                  |
| other\_branches | Do not capture jmp/call ptr\*, jmp/call m\*, ret (0c8h), sys\*, interrupts, exceptions (other than debug exceptions), iret, int3, intn, into, tsx abort, eenter, eresume, eexit, aex, init, sipi, rsm (only in ARCH LBR)                          |
| call\_stack     | Enable LBR stack to use LIFO filtering to capture call stack profile. Not available on CPUs older than Haswell. For this option, you can only additionally specify `user` or `kernel`. It prevents all types of branches except calls and returns |

### Examples

The following command enables the Last Branch Record.

```c
HyperDbg> !lbr enable
```

The following command disables the Last Branch Record.

```c
HyperDbg> !lbr disable
```

The following command flushes (clears) the Last Branch Record.

```c
HyperDbg> !lbr flush
```

The following command applies the default LBR configuration in which all types of branches are shown or in other words, it just resets the filter options to include all branches.

```c
HyperDbg> !lbr filter
```

The following command configures the LBR filter to not capture kernel-mode conditional branches, indirect jumps, relative jumps, and far branches.

```c
HyperDbg> !lbr filter kernel jcc ind_jmp rel_jmp far
```

The following command configures the LBR filter to not capture kernel-mode conditional branches, near returns, indirect jumps, relative jumps, and far branches.

```c
HyperDbg> !lbr filter kernel jcc return ind_jmp rel_jmp far
```

The following command configures the LBR filter to not capture kernel-mode conditional branches, indirect jumps, and relative jumps.

```c
HyperDbg> !lbr filter kernel jcc ind_jmp rel_jmp
```

The following command configures the LBR filter to not capture user-mode relative calls, indirect calls, near returns, and far branches.

```c
HyperDbg> !lbr filter user rel_call ind_call return far
```

The following command enables the call stack profile mode for user-mode branches.

```c
HyperDbg> !lbr filter call_stack user
```

The following command enables the call stack profile mode for kernel-mode branches.

```c
HyperDbg> !lbr filter call_stack kernel
```

### SDK

None

### Remarks

Starting from **v0.19**, this command was added to the HyperDbg debugger.

Intel processors support two LBR implementations. **Legacy LBR** uses model-specific registers (MSRs) and is available on processors up to and including the **11th generation**. **Architectural LBR (ARCH LBR)** was introduced starting from the **12th generation** and exposes LBR as a standardized set of architectural registers.

Some filter options (e.g., `far`) are only available in Legacy LBR, while others (e.g., `other_branches`) are only available in ARCH LBR.

The LBR is a ring buffer that records control flow transitions, but its limited depth can be a problem when profiling code that makes many small helper (leaf) function calls, such as in C++. These short calls fill up the buffer quickly and push out the more important call stack context before it can be examined. The `call_stack` mode solves this by applying LIFO (last-in, first-out) filtering: calls are recorded normally, but when a **RET** is executed, the matching **CALL** entry is removed from the buffer. This effectively cancels out leaf function call/return pairs, keeping the buffer focused on the main execution path rather than short-lived helper calls.

When using the `call_stack` option, it is recommended to also specify either `user` or `kernel` to restrict profiling to the desired privilege level. Note that `call_stack` removes all branch types from the LBR except **CALL** and **RET** instructions; all other branch types (conditional jumps, indirect jumps, etc.) are not captured.

On a machine that supports Architectural LBR, there is additional information available for each branch entry, such as the branch type and whether the cycle count is valid. See the '[!lbrdump](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)' command for an example of these differences.

Be aware that a Debug Break (#DB) exception can disable LBR on the affected core. This can happen when using stepping commands such as '[t](https://docs.hyperdbg.org/commands/debugging-commands/t)' (step-in), or '[p](https://docs.hyperdbg.org/commands/debugging-commands/p)', or other commands that internally rely on the trap flag (TF) or hardware debug registers (DR0–DR3). If LBR is disabled on a core as a result, you can use '[lbr\_check](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_check)' to detect the condition and '[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)' or '[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)' to re-enable it from within a script.

If you want to prevent LBR from being masked and disabled by the processor automatically (e.g., by a #DB), and you are using the VMM module, you can use the '[!exception](https://docs.hyperdbg.org/commands/extension-commands/exception)' command to intercept Debug Breaks (#DB). Simply intercepting them is sufficient as the CPU will not mask LBR again. For example, the following event command prevents LBR from being disabled:

```c
!exception 1 script {
	printf("Process ID: %x (name: %s) tried to disable LBR using a #DB at: %llx (core id: %x) and it is ignored\n", $pid, $pname, @rip, $core);
}
```

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_save)

[lbr\_print](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_print)

[lbr\_check](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_check)

[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)

[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)

[!lbrdump (dump Last Branch Record entries)](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)
