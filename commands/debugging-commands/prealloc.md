---
description: Description of 'prealloc' command in HyperDbg.
---

# prealloc (reserve pre-allocated pools)

### Command

> prealloc

### Syntax

> prealloc \[type (monitor)] \[count (hex value)]

### Description

Reserves a specified number of pre-allocated pools.

### Parameters

**\[type (monitor)]**

The type of pools to be reserved

**\[count (hex value)]**

Number of pools to be allocated and reserved

### Examples

Imagine we want to pre-allocate and reserve **0x10** number of pools for the '[!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor)' command.

```
0: kHyperDbg> prealloc monitor 10
the requested pools are allocated and reserved
```

### IOCTL



### Remarks

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

None
