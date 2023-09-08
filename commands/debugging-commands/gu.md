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
0: kHyperDbg> p
fffff801`68d91267    41 5B                               pop r11
```

### IOCTL

The instruction for this command is the same as the ['p' command's IOCTL](https://docs.hyperdbg.org/commands/debugging-commands/p#ioctl) except it checks whether the instruction is equal to **RET** or not.

### Remarks

This command is the same as the '[p (step-over)](https://docs.hyperdbg.org/commands/debugging-commands/p)' except it checks whether the instruction is equal to **RET** or not.

All cores and threads (except the currently executing thread) find a chance to be executed between each step in this type of stepping.

### Requirements

None

### Related

[p (step-over)](https://docs.hyperdbg.org/commands/debugging-commands/p)

[t (step-in)](https://docs.hyperdbg.org/commands/debugging-commands/t)

[i (instrumentation step-in)](https://docs.hyperdbg.org/commands/debugging-commands/i)
