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

None

### Examples

First, enable the LBR using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command, then use `lbr_save()` in a script (e.g., inside an EPT hook), and finally disable the LBR.

```clike
!lbr enable

!epthook 7ff7393a2fd7 pid 3274 script {
		lbr_save();
}

!lbr disable
```

The above example enables LBR, hooks the target address in process with PID **3274**, and each time execution reaches that address, the captured branch entries are saved. You can then use '[!lbrdump](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)' to inspect them.

### Remarks

The support for this function is added from **v0.19**.

Before calling this function, you need to enable the Last Branch Record using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command.

### Related

[lbr\_print](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr\_print)

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)

[!lbrdump (dump Last Branch Record entries)](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)
