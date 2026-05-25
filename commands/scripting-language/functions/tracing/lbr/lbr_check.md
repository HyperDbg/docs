---
description: Description of the 'lbr_check' function in HyperDbg Scripts
---

# lbr\_check

### Function

> lbr\_check

### Syntax

> lbr\_check();

### Parameters

None

### Description

Checks whether the Last Branch Record (LBR) is currently enabled on the **current core**. The state of other cores is not checked.

This function is useful before calling '[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_save)' or '[lbr\_print](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_print)', because certain debug events, such as trap flag exceptions (\#DB) or hardware debug register breakpoints (DR0–DR3) that are used for stepping through instructions can inadvertently disable LBR on the core where they fire. If `lbr_check()` returns zero, you can use '[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)' or '[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)' to re-enable it. Note that after restoring, execution must resume and the event must trigger again before the LBR buffer contains valid data.

### Return value

Returns **1** if LBR is currently enabled on the current core, or **0** if it has been disabled (e.g., due to a \#DB exception triggered by a trap flag or a hardware debug register).

### Examples

The following example checks whether LBR is still enabled before saving entries. If it has been disabled (e.g., by a \#DB), `lbr_restore()` re-enables it with the previously configured filter, and execution continues until the next trigger to collect valid data.

```c
!lbr enable

!epthook 7ff7393a2fd7 pid 3274 script {
        if (lbr_check() == 0) {
            lbr_restore();
            printf("LBR is disabled (e.g., because of a #DB) and now it is re-enabled.\n");
        } else {
            lbr_save();
            printf("LBR records are saved.\n");
        }
}
```

### Remarks

The support for this function is added from **v0.19**.

This function only checks the LBR state of the **current core**. Each core maintains its own LBR state independently.

Debug Break (\#DB) exceptions raised by trap flags or hardware debug registers can disable LBR on the affected core. Use this function to detect that condition and restore LBR with '[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)' or '[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)'.

### Related

[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)

[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)

[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_save)

[lbr\_print](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_print)

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)

[!lbrdump (dump Last Branch Record entries)](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)
