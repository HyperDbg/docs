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

Returns **1** if the LBR entries were printed successfully, or **0** if the operation failed (e.g., LBR was not initialized using the '[!lbr enable](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command or was already disabled using '[!lbr disable](https://docs.hyperdbg.org/commands/extension-commands/lbr)').

### Examples

First, enable the LBR using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command, then use `lbr_print()` in a script (e.g., inside an EPT hook), and finally disable the LBR.

```c
!lbr enable

!epthook 7ff7393a2fd7 pid 3274 script {
		lbr_print();
}

!lbr disable
```

The above example enables LBR, hooks the target address in process with PID **0x3274**, and each time execution reaches that address, all captured branch entries are printed to the output.

### Remarks

The support for this function is added from **v0.19**.

This function requires LBR to be active. It will return **0** and perform no operation if LBR was never started with '[!lbr enable](https://docs.hyperdbg.org/commands/extension-commands/lbr)' or has already been stopped with '[!lbr disable](https://docs.hyperdbg.org/commands/extension-commands/lbr)'.

`lbr_dump` is an alias for this function and behaves identically.

### Related

[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_save)

[lbr\_check](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_check)

[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)

[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)

[!lbrdump (dump Last Branch Record entries)](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)
