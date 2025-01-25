---
description: Description of the '!unhide' command in HyperDbg.
---

# !unhide (disable transparent-mode)

### Command

> !unhide

### Syntax

> !unhide

### Description

Disables the transparent-mode of **HyperDbg**.

### Parameters

None

### Examples

```
HyperDbg> !unhide
```

### SDK

To disable the transparent mode, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_disable_transparent_mode();
```

### Remarks

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

[!hide (enable transparent mode)](https://docs.hyperdbg.org/commands/extension-commands/hide)
