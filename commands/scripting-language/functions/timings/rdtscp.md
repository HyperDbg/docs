---
description: Description of the 'rdtscp' function in HyperDbg Scripts
---

# rdtscp

### Function

> rdtscp

### Syntax

> rdtscp();

### Parameters

None

### Description

Reads and returns the current value of the processor's (current core's) time-stamp counter using the `RDTSCP` instruction.

### Return value

The time-stamp counter value (the value of `EDX:EAX`) returned as a 64-bit value.

### Examples

The following code executes and returns the TSC value.

`? current_core_tsc = rdtscp();`

### Remarks

None

### Related
