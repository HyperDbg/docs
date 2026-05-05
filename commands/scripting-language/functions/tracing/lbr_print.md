---
description: Description of the 'lbr_print' function in HyperDbg Scripts
---

# lbr\_print

### Function

> lbr\_print

### Syntax

> lbr\_print();

### Parameters

None

### Description

Prints all the Last Branch Record (LBR) entries that were captured since the Last Branch Record was enabled using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command.

### Return value

None

### Examples

First, enable the LBR using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command, then use `lbr_print()` in a script (e.g., inside an EPT hook), and finally disable the LBR.

```clike
!lbr enable

!epthook 7ff7393a2fd7 pid 3274 script {
		lbr_print();
}

!lbr disable
```

The above example enables LBR, hooks the target address in process with PID **0x3274**, and each time execution reaches that address, all captured branch entries are printed to the output.

### Remarks

The support for this function is added from **v0.19**.

Before calling this function, you need to enable the Last Branch Record using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command.

### Related

[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr\_save)

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)

[!lbrdump (dump Last Branch Record entries)](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)
