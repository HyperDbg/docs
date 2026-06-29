---
description: Description of the 'lbr_restore' function in HyperDbg Scripts
---

# lbr\_restore

### Function

> lbr\_restore

### Syntax

> lbr\_restore();

### Parameters

None

### Description

Re-enables the Last Branch Record (LBR) on the **current core** using the filter options that were previously configured via the '[!lbr filter](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command.

This function exists because certain debug events, such as trap flag exceptions (#DB) or hardware debug register breakpoints (DR0–DR3) can disable LBR on the core where they fire. You can use '[lbr\_check](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_check)' to detect this condition before calling this function. After restoring, execution must resume and the event must trigger again before the LBR buffer contains valid data.

### Return value

Returns **1** if LBR was restored successfully, or **0** if the operation failed (e.g., LBR was not initialized using the '[!lbr enable](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command or was already disabled using '[!lbr disable](https://docs.hyperdbg.org/commands/extension-commands/lbr)').

### Examples

The following example restores LBR with the previously configured filter if it has been disabled by a #DB event.

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

This function restores LBR using the filter configuration from the most recent '[!lbr filter](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command. If you need to restore with a specific filter bitmask instead, use '[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)'.

To use this function, the trace module should be loaded using the [load](https://docs.hyperdbg.org/commands/debugging-commands/load) command (`load trace`).

This function requires LBR to have been previously initialized with '[!lbr enable](https://docs.hyperdbg.org/commands/extension-commands/lbr)'. It will return **0** and perform no operation if LBR was never started or has already been stopped with '[!lbr disable](https://docs.hyperdbg.org/commands/extension-commands/lbr)'.

Debug Break (#DB) exceptions raised by trap flags or hardware debug registers can disable LBR on the affected core. After calling this function, execution must resume and the target event must fire again to capture valid LBR data.

### Related

[lbr\_check](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_check)

[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)

[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_save)

[lbr\_print](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_print)

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)

[!lbrdump (dump Last Branch Record entries)](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)
