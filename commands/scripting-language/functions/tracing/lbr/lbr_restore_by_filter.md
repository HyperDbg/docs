---
description: Description of the 'lbr_restore_by_filter' function in HyperDbg Scripts
---

# lbr\_restore\_by\_filter

### Function

> lbr\_restore\_by\_filter

### Syntax

> lbr\_restore\_by\_filter(FilterOptions);

### Parameters

**\[FilterOptions (QWORD)]**

A bitmask that specifies which branch types are **not** captured. Passing `0` captures all branch types (default behavior). Each bit in the value controls a specific filter:

| Bit | Hex Value | Description                                                                                    |
| --- | --------- | ---------------------------------------------------------------------------------------------- |
| 0   | 0x001     | Do not capture at ring 0 (kernel)                                                              |
| 1   | 0x002     | Do not capture at ring > 0 (user)                                                              |
| 2   | 0x004     | Do not capture conditional branches                                                            |
| 3   | 0x008     | Do not capture relative calls                                                                  |
| 4   | 0x010     | Do not capture indirect calls                                                                  |
| 5   | 0x020     | Do not capture near returns                                                                    |
| 6   | 0x040     | Do not capture indirect jumps                                                                  |
| 7   | 0x080     | Do not capture relative jumps                                                                  |
| 8   | 0x100     | Do not capture far branches (Legacy LBR) / other branches (ARCH LBR)                          |
| 9   | 0x200     | Enable call stack mode - LIFO filtering to capture call stack profile (not available on all CPUs) |

{% hint style="info" %}
For call stack profiling mode, bits 2, 6, 7, 8, and 9 must be set together (combined value `0x3C4`). This base configuration enables call stack filtering while suppressing all branch types other than calls and returns. You can additionally set bit 0 (`0x001`) to restrict recording to kernel mode only, or bit 1 (`0x002`) to restrict to user mode only.
{% endhint %}

### Description

Re-enables the Last Branch Record (LBR) on the **current core** with the specified filter options provided as a bitmask.

This function is similar to '[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)', but instead of reusing the filter previously set by '[!lbr filter](https://docs.hyperdbg.org/commands/extension-commands/lbr)', it accepts a custom filter bitmask directly. This is useful when you need fine-grained control over which branches are captured at restore time or if you want to have different filters for different cores.

This function exists because certain debug events, such as trap flag exceptions (\#DB) or hardware debug register breakpoints (DR0–DR3) can disable LBR on the core where they fire. You can use '[lbr\_check](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_check)' to detect this condition before calling this function. After restoring, execution must resume and the event must trigger again before the LBR buffer contains valid data.

### Return value

Returns **1** if LBR was restored successfully, or **0** if the operation failed (e.g., LBR was not initialized using the '[!lbr enable](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command or was already disabled using '[!lbr disable](https://docs.hyperdbg.org/commands/extension-commands/lbr)').

### Examples

The following example restores LBR with no filtering (capture all branch types) if it has been disabled.

```c
!lbr enable

!epthook 7ff7393a2fd7 pid 3274 script {
        if (lbr_check() == 0) {
            lbr_restore_by_filter(0);
            printf("LBR is disabled (e.g., because of a #DB) and now it is re-enabled.\n");
        } else {
            lbr_save();
            printf("LBR records are saved.\n");
        }
}
```

The following example restores LBR in call stack profiling mode restricted to user-mode branches. The value `0x3c6` combines bits 1 (user), 2 (jcc), 6 (ind\_jmp), 7 (rel\_jmp), 8 (far/other), and 9 (call\_stack).

```c
!lbr enable

!epthook 7ff7393a2fd7 pid 3274 script {
        if (lbr_check() == 0) {
            lbr_restore_by_filter(0x3c6);
            printf("LBR is disabled (e.g., because of a #DB) and now it is re-enabled.\n");
        } else {
            lbr_save();
            printf("LBR records are saved.\n");
        }
}
```

### Remarks

The support for this function is added from **v0.19**.

This function requires LBR to have been previously initialized with '[!lbr enable](https://docs.hyperdbg.org/commands/extension-commands/lbr)'. It will return **0** and perform no operation if LBR was never started or has already been stopped with '[!lbr disable](https://docs.hyperdbg.org/commands/extension-commands/lbr)'.

Debug Break (\#DB) exceptions raised by trap flags or hardware debug registers can disable LBR on the affected core. After calling this function, execution must resume and the target event must fire again to capture valid LBR data.

### Related

[lbr\_check](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_check)

[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)

[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_save)

[lbr\_print](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_print)

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)

[!lbrdump (dump Last Branch Record entries)](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)
