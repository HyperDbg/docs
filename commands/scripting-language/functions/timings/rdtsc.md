---
description: Description of the 'rdtsc' function in HyperDbg Scripts
---

# rdtsc

### Function

> rdtsc

### Syntax

> rdtsc();

### Parameters

None

### Description

Reads and returns the current value of the processor's (current core's) time-stamp counter using the `RDTSC` instruction.

### Return value

The time-stamp counter value (the value of `EDX:EAX`) returned as a 64-bit value.

### Examples

The following code executes and returns the TSC value.

`? current_core_tsc = rdtsc();`

### Remarks

None

### Related

[rdtscp](https://docs.hyperdbg.org/commands/scripting-language/functions/timings/rdtscp)

