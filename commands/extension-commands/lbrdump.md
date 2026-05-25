---
description: Description of '!lbrdump' command in HyperDbg.
---

# !lbrdump (dump Last Branch Record entries)

### Command

> !lbrdump

### Syntax

> !lbrdump \[core CoreId (hex)]

### Description

Dumps the Last Branch Record (LBR) entries.

{% hint style="info" %}
Before using this command, you need to enable the LBR using the '[!lbr](https://docs.hyperdbg.org/commands/extension-commands/lbr)' command.
{% endhint %}

### Parameters

**\[core CoreId (hex)] (optional)**

Optional value to dump the LBR entries from a specific core. Add `core xx` to your command; thus, the LBR entries from core `xx` will be dumped. If you don't specify this option, then by default, the LBR entries from all cores are dumped.

### Examples

The following command dumps LBR entries from all cores. Note that in this case, only the second core has the target entries; in other words, only the second core executed the '[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr_save)' function as a result of triggering an event.

```c
HyperDbg> !lbrdump
LBR Chronological Trace on core (decimal): 0

         no LBR entries found for this core
         you can use the 'lbr_save();' function in the script engine
         on the target core to save the LBR entries before dumping

         ===========================================================

LBR Chronological Trace on core (decimal): 1

         no LBR entries found for this core
         you can use the 'lbr_save();' function in the script engine
         on the target core to save the LBR entries before dumping

         ===========================================================

LBR Chronological Trace on core (decimal): 2

         [29] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [30] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [31] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [ 0] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [ 1] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [ 2] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [ 3] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [ 4] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [ 5] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [ 6] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [ 7] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [ 8] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [ 9] Branch Mispredicted: false, Cycle Count (Decimal): 008 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [10] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [11] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [12] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [13] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [14] Branch Mispredicted: false, Cycle Count (Decimal): 008 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [15] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [16] Branch Mispredicted: false, Cycle Count (Decimal): 013 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [17] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [18] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [19] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [20] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [21] Branch Mispredicted: false, Cycle Count (Decimal): 004 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [22] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [23] Branch Mispredicted: false, Cycle Count (Decimal): 004 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [24] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [25] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [26] Branch Mispredicted: true , Cycle Count (Decimal): 001 - From: 00007ff76c3a8d54  To: 00007ff76c3a8eef
         [27] Branch Mispredicted: false, Cycle Count (Decimal): 007 - From: 00007ff76c3a8efb  To: 00007ff76c3a913e
         [28] Branch Mispredicted: false, Cycle Count (Decimal): 015 - From: 00007ff76c3a9144  To: 00007ff76c3a2fd7
LBR Chronological Trace on core (decimal): 3

         no LBR entries found for this core
         you can use the 'lbr_save();' function in the script engine
         on the target core to save the LBR entries before dumping

         ===========================================================

LBR Chronological Trace on core (decimal): 4

         no LBR entries found for this core
         you can use the 'lbr_save();' function in the script engine
         on the target core to save the LBR entries before dumping

         ===========================================================
...
```

The following command dumps LBR entries from core **0x2**.

```c
HyperDbg> !lbrdump core 2
LBR Chronological Trace on core (decimal): 2

         [29] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [30] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [31] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [ 0] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [ 1] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [ 2] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [ 3] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [ 4] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [ 5] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [ 6] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [ 7] Branch Mispredicted: false, Cycle Count (Decimal): 005 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [ 8] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [ 9] Branch Mispredicted: false, Cycle Count (Decimal): 008 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [10] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [11] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [12] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [13] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [14] Branch Mispredicted: false, Cycle Count (Decimal): 008 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [15] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [16] Branch Mispredicted: false, Cycle Count (Decimal): 013 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [17] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [18] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [19] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8d6a  To: 00007ff76c3a8de5
         [20] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8df5  To: 00007ff76c3a8e13
         [21] Branch Mispredicted: false, Cycle Count (Decimal): 004 - From: 00007ff76c3a8e25  To: 00007ff76c3a8e46
         [22] Branch Mispredicted: false, Cycle Count (Decimal): 002 - From: 00007ff76c3a8e61  To: 00007ff76c3a8e8a
         [23] Branch Mispredicted: false, Cycle Count (Decimal): 004 - From: 00007ff76c3a8e9f  To: 00007ff76c3a8eb1
         [24] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eba  To: 00007ff76c3a8eea
         [25] Branch Mispredicted: false, Cycle Count (Decimal): 001 - From: 00007ff76c3a8eea  To: 00007ff76c3a8d44
         [26] Branch Mispredicted: true , Cycle Count (Decimal): 001 - From: 00007ff76c3a8d54  To: 00007ff76c3a8eef
         [27] Branch Mispredicted: false, Cycle Count (Decimal): 007 - From: 00007ff76c3a8efb  To: 00007ff76c3a913e
         [28] Branch Mispredicted: false, Cycle Count (Decimal): 015 - From: 00007ff76c3a9144  To: 00007ff76c3a2fd7
```

On a machine that supports Architectural LBR, there is additional information available for each branch entry, such as the branch type and whether the cycle count is valid. For example, on an Architectural LBR machine:

```c
HyperDbg> !lbrdump core 9
 LBR Chronological Trace on core (decimal): 9
         [ 0] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 1442 (is valid? true ) - From: fffff802ec3ef7c1  To: fffff802ec6b3126
         [ 1] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0084 (is valid? true ) - From: fffff802ec6b3121  To: fffff802ec3ef6c0
         [ 2] Branch Mispredicted: true , Branch type: RET          , Cycle Count (Decimal): 0075 (is valid? true ) - From: fffff802ec8c3ac2  To: fffff802ec6b3055
         [ 3] Branch Mispredicted: true , Branch type: RET          , Cycle Count (Decimal): 0020 (is valid? true ) - From: fffff802ec8c4454  To: fffff802ec8c3abe
         [ 4] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0003 (is valid? true ) - From: fffff802ec69a704  To: fffff802ec8c4442
         [ 5] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0066 (is valid? true ) - From: fffff802ec8c443d  To: fffff802ec69a6f0
         [ 6] Branch Mispredicted: true , Branch type: RET          , Cycle Count (Decimal): 0028 (is valid? true ) - From: fffff802ec8c5645  To: fffff802ec8c4432
         [ 7] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0003 (is valid? true ) - From: fffff802ec69a704  To: fffff802ec8c562e
         [ 8] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0052 (is valid? true ) - From: fffff802ec8c5629  To: fffff802ec69a6f0
         [ 9] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0009 (is valid? true ) - From: fffff802ec2861a0  To: fffff802ec8c5688
         [10] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0012 (is valid? true ) - From: fffff802ec29b9cd  To: fffff802ec286189
         [11] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0011 (is valid? true ) - From: fffff802ec29ba0f  To: fffff802ec29b9c9
         [12] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0001 (is valid? true ) - From: fffff802ec29b9c4  To: fffff802ec29b9e0
         [13] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0020 (is valid? true ) - From: fffff802ec286184  To: fffff802ec29b9c0
         [14] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0036 (is valid? true ) - From: fffff802ec29bac1  To: fffff802ec286148
         [15] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0145 (is valid? true ) - From: fffff802ec29d939  To: fffff802ec29bae8
         [16] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0018 (is valid? true ) - From: fffff802ec2e2e2d  To: fffff802ec29d63a
         [17] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0428 (is valid? true ) - From: fffff802ec29d635  To: fffff802ec2e2a20
         [18] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0153 (is valid? true ) - From: fffff802ec29bae3  To: fffff802ec29d4a0
         [19] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0100 (is valid? true ) - From: fffff802ec286143  To: fffff802ec29ba20
         [20] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0810 (is valid? true ) - From: fffff802ec8c5683  To: fffff802ec285ff0
         [21] Branch Mispredicted: true , Branch type: RET          , Cycle Count (Decimal): 0648 (is valid? true ) - From: fffff802ec29c9c3  To: fffff802ec8c57a0
         [22] Branch Mispredicted: true , Branch type: RET          , Cycle Count (Decimal): 0102 (is valid? true ) - From: fffff802ec29e33c  To: fffff802ec29cdbf
         [23] Branch Mispredicted: true , Branch type: RET          , Cycle Count (Decimal): 0064 (is valid? true ) - From: fffff802ec31d5fe  To: fffff802ec29e13d
         [24] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0006 (is valid? true ) - From: fffff802ec28674a  To: fffff802ec31d76b
         [25] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0001 (is valid? true ) - From: fffff802ec2a030d  To: fffff802ec2869e9
         [26] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0021 (is valid? true ) - From: fffff802ec2bc8a5  To: fffff802ec2a02fb
         [27] Branch Mispredicted: false, Branch type: CALL Direct  , Cycle Count (Decimal): 0004 (is valid? true ) - From: fffff802ec2a02f6  To: fffff802ec2bc860
         [28] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0002 (is valid? true ) - From: fffff802ec250c11  To: fffff802ec2a02c5
         [29] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0005 (is valid? true ) - From: fffff802ecb634ab  To: fffff802ec250c8c
         [30] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0001 (is valid? true ) - From: fffff802ec2e091c  To: fffff802ecb63528
         [31] Branch Mispredicted: false, Branch type: RET          , Cycle Count (Decimal): 0075 (is valid? true ) - From: fffff802ec6a8634  To: fffff802ec2e0900
```

### IOCTL

None

### Remarks

Starting from **v0.19**, this command was added to the HyperDbg debugger.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[lbr\_save](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_save)

[lbr\_print](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_print)

[lbr\_check](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_check)

[lbr\_restore](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore)

[lbr\_restore\_by\_filter](https://docs.hyperdbg.org/commands/scripting-language/functions/tracing/lbr/lbr_restore_by_filter)

[!lbr (enable, disable, and configure Last Branch Record)](https://docs.hyperdbg.org/commands/extension-commands/lbr)
