---
description: Description of the 'microsleep' function in HyperDbg Scripts
---

# microsleep

### Function

> microsleep

### Syntax

> microsleep( MicroSecond );

### Parameters

**\[MicroSecond]**

The time to sleep (pause the execution) in microseconds.

### Description

Sleeps (pauses the execution) the core execution for a specified duration in microseconds..

### Return value

None

### Examples

The following code uses the `microsleep` function to pause execution for 100 microseconds each time the hook is triggered at the target function.

```clike
!epthook 0x7ff129000 script {
    microsleep(100); // pause (sleep) the execution for 100 microseconds
}
```

### Remarks

This function pauses only the core on which it is executed. For example, if used within an event, it will pause only the executing core, not any others.

### Related

None
