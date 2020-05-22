---
description: Description of 'cpu' command in HyperDbg.
---

# cpu \(check cpu supported technologies\)

### Command

> cpu

### Syntax

> cpu

### Description

Shows the current supported technologies on processor based on the details provided by `cpuid` instruction.

### Parameters

None

### Examples

The following examples show a sample of `cpu` results.

```text
HyperDbg >cpu

vendor : GenuineIntel
brand : Intel(R) Core(TM) i7-6820HQ CPU @ 2.70GHz

3DNOW not supported
3DNOWEXT not supported
ABM not supported
ADX supported
AES supported
AVX supported
AVX2 supported
AVX512CD not supported
AVX512ER not supported
                                ...
```

### IOCTL

This function is completely a user mode function, there is not kernel IOCTL for it.

### **Remarks**

1. If you didn't attach to any remote sessions then it will show the current systems details.

### Requirements

None

### Related

None

