---
description: Description of '!smi' command in HyperDbg.
---

# !smi (trigger and show System Management Interrupt functionalities)

> !smi

### Syntax

> !smi \[Function (string)]

### Description

Triggers and shows functionalities related to System Management Interrupts (SMIs).

### Parameters

**\[Function (string)]**

The target functionality.

| Function | Description                                   |
| -------- | --------------------------------------------- |
| count    | Number of SMIs triggered in the target system |
| trigger  | Triggers a power SMI                          |

### Examples

The following command triggers a power SMI.

```c
HyperDbg> !smi trigger
power SMI triggered successfully (you can use '!smi count' to view the number of executed SMIs)
```

The following example shows the number of SMIs that are already executed in the target system.

```clike
HyperDbg> !smi count
SMI count: 0x153e
```

### SDK

To perform SMI-related functionalities, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_perform_smi_operation(SMI_OPERATION_PACKETS * SmiOperation);
```

### Remarks

Starting from **v0.15**, this command was added to the HyperDbg debugger.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

HyperDbg reads the number of SMIs from `MSR_SMI_COUNT` MSR (**0x34**) and triggers SMIs using **APM I/O Decode Registers**. For more information, please refer to this [post](https://nixhacker.com/digging-into-smm/).

### Requirements

None

### Related

None
