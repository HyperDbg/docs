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

Once triggered, you will see some results similar to this (on a [**Legacy** LBR](https://docs.hyperdbg.org/commands/extension-commands/lbr#requirements) machine):

```c
LBR Chronological Trace on core : 0x2

         [20] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8eba  To: 00007ff7808c8eea
         [21] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8eea  To: 00007ff7808c8d44
         [22] Branch Mispredicted: false, Cycle Count (Decimal): 0002 - From: 00007ff7808c8d6a  To: 00007ff7808c8de5
         [23] Branch Mispredicted: false, Cycle Count (Decimal): 0004 - From: 00007ff7808c8e11  To: 00007ff7808c8e5d
         [24] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8e61  To: 00007ff7808c8e8a
         [25] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8e8e  To: 00007ff7808c8ebc
         [26] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8ec0  To: 00007ff7808c8ecb
         [27] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8ecf  To: 00007ff7808c8ee1
         [28] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8eea  To: 00007ff7808c8d44
         [29] Branch Mispredicted: false, Cycle Count (Decimal): 0002 - From: 00007ff7808c8d6a  To: 00007ff7808c8de5
         [30] Branch Mispredicted: false, Cycle Count (Decimal): 0004 - From: 00007ff7808c8e11  To: 00007ff7808c8e5d
         [31] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8e61  To: 00007ff7808c8e8a
         [ 0] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8e8e  To: 00007ff7808c8ebc
         [ 1] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8ec0  To: 00007ff7808c8ecb
         [ 2] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8ecf  To: 00007ff7808c8ee1
         [ 3] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8eea  To: 00007ff7808c8d44
         [ 4] Branch Mispredicted: false, Cycle Count (Decimal): 0002 - From: 00007ff7808c8d6a  To: 00007ff7808c8de5
         [ 5] Branch Mispredicted: false, Cycle Count (Decimal): 0004 - From: 00007ff7808c8e11  To: 00007ff7808c8e5d
         [ 6] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8e61  To: 00007ff7808c8e8a
         [ 7] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8e8e  To: 00007ff7808c8ebc
         [ 8] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8ec0  To: 00007ff7808c8ecb
         [ 9] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8ecf  To: 00007ff7808c8ee1
         [10] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8eea  To: 00007ff7808c8d44
         [11] Branch Mispredicted: false, Cycle Count (Decimal): 0002 - From: 00007ff7808c8d6a  To: 00007ff7808c8de5
         [12] Branch Mispredicted: false, Cycle Count (Decimal): 0004 - From: 00007ff7808c8e11  To: 00007ff7808c8e5d
         [13] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8e61  To: 00007ff7808c8e8a
         [14] Branch Mispredicted: false, Cycle Count (Decimal): 0023 - From: 00007ff7808c8e9f  To: 00007ff7808c8eb1
         [15] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8eba  To: 00007ff7808c8eea
         [16] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8eea  To: 00007ff7808c8d44
         [17] Branch Mispredicted: true , Cycle Count (Decimal): 0001 - From: 00007ff7808c8d54  To: 00007ff7808c8eef
         [18] Branch Mispredicted: false, Cycle Count (Decimal): 0001 - From: 00007ff7808c8efb  To: 00007ff7808c913e
         [19] Branch Mispredicted: false, Cycle Count (Decimal): 0015 - From: 00007ff7808c9144  To: 00007ff7808c2fd7
```

Or this output if you are using an [**Architectural** LBR ](https://docs.hyperdbg.org/commands/extension-commands/lbr#requirements)machine:

```c
LBR Chronological Trace on core: 0x9
 
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
