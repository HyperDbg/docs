---
description: Description of the 'lbr_save' function in HyperDbg Scripts
---

# lbr\_save

### Function

> lbr\_save

### Syntax

> lbr\_save();

### Parameters

None

### Description

Saves the Last Branch Record (LBR) entries that were captured since the Last Branch Record was enabled using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command. The entries are stored internally and can later be viewed using the '[!lbrdump](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)' command. Unlike `lbr_print()`, this function does not display the entries immediately.

{% hint style="warning" %}
If `lbr_save()` is called more than once on the same core, the previously saved entries for that core are replaced with the new ones.
{% endhint %}

### Return value

Returns **1** if the LBR entries were saved successfully, or **0** if the operation failed (e.g., LBR was not initialized using the '[!lbr enable](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command or was already disabled using '[!lbr disable](https://docs.hyperdbg.org/commands/extension-commands/lbr)').

### Examples

First, enable the LBR using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command, then use `lbr_save()` in a script (e.g., inside an EPT hook), and finally disable the LBR.

```c
!lbr enable

!epthook 7ff7393a2fd7 pid 3274 script {
		lbr_save();
}

!lbr disable
```

The above example enables LBR, hooks the target address in process with PID **0x3274**, and each time execution reaches that address, the captured branch entries are saved. You can then use '[!lbrdump](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)' to inspect them.

### Remarks

The support for this function is added from **v0.19**.

This function requires LBR to be active. It will return **0** and perform no operation if LBR was never started with '[!lbr enable](https://docs.hyperdbg.org/commands/extension-commands/lbr)' or has already been stopped with '[!lbr disable](https://docs.hyperdbg.org/commands/extension-commands/lbr)'.

### Related

[lbr\_print](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_print)

[lbr\_check](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_check)

[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)

[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)

[!lbrdump (dump Last Branch Record entries)](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)
