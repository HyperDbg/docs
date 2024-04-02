---
description: Description of the 'cpu' command in HyperDbg.
---

# cpu (check cpu supported technologies)

### Command

> cpu

### Syntax

> cpu

### Description

Shows the currently supported technologies on the processor based on the details provided by `cpuid` instruction.

### Parameters

None

### Examples

The following examples show a sample of `cpu` results.

```
HyperDbg> cpu

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

This function is completely a user-mode function. There is not kernel IOCTL for it.

### Remarks

If you didn't attach to any remote sessions, it would show the current system's details.

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

None
