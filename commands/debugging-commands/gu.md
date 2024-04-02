---
description: Description of the 'gu' command in HyperDbg.
---

# gu (step-out or go up)

### Command

> gu

### Syntax

> gu
>
> gu \[Count (hex)]

### Description

Executes instructions one by one until one **RET** instruction is executed (step-out or go up).

### Parameters

**\[Count (hex)] (optional)**

The number of **instructions** to perform the instrument (The execution might not meet a RET instruction if not enough number is specified).

### Examples

If you want to step-out or go up from the current function.

```
4: kHyperDbg> gu
ntkrnlmp!KiSystemServiceCopyEnd+0x25:
fffff802`2d045fe5    0F 1F 00                            nop dword ptr ds:[rax], eax
```

### IOCTL

The instruction for this command is the same as the ['p' command's IOCTL](https://docs.hyperdbg.org/commands/debugging-commands/p#ioctl) except it checks whether the instruction is equal to **RET** or not and sets the `StepRequestType` to `DEBUGGER_REMOTE_STEPPING_REQUEST_STEP_OVER_FOR_GU`.

### Remarks

Starting from **v0.6**, this command was added to the HyperDbg debugger.

This command is the same as the '[p (step-over)](https://docs.hyperdbg.org/commands/debugging-commands/p)' except it checks whether the instruction is equal to **RET** or not.

All cores and threads (except the currently executing thread) find a chance to be executed between each step in this command.

### Requirements

None

### Related

[k, kd, kq (display stack backtrace)](https://docs.hyperdbg.org/commands/debugging-commands/k)

[p (step-over)](https://docs.hyperdbg.org/commands/debugging-commands/p)

[t (step-in)](https://docs.hyperdbg.org/commands/debugging-commands/t)

[i (instrumentation step-in)](https://docs.hyperdbg.org/commands/debugging-commands/i)
