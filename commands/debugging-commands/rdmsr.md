---
description: Description of 'rdmsr' command in HyperDbg.
---

# rdmsr \(read model-specific register\)

### Command

> rdmsr

### Syntax

> rdmsr \[msr \(hex\) - ecx\] core \[core number\(hex\)\]

### Description

Reads the model-specific register using '**rdmsr**' instruction.

### Parameters

\[msr \(hex\) - ecx\]

          The index of MSR \(ECX Register for '**rdmsr**' instruction\)

core \[core number\(hex\)\] \(optional\)

          The core that we want to read the 'rdmsr' from

{% hint style="success" %}
If you don't specify the 'core' by default, it shows the MSR for all cores.
{% endhint %}

### Examples

The following command shows the MSR register for `c0000082` using `rdmsr` command.

```diff
HyperDbg> rdmsr c0000082
core : 0x0 - msr[c0000082] = fffff807`73553180
core : 0x1 - msr[c0000082] = fffff807`73553180
core : 0x2 - msr[c0000082] = fffff807`73553180
core : 0x3 - msr[c0000082] = fffff807`73553180
```

The following example shows the MSR `c0000082` for core `2`.

```diff
HyperDbg> rdmsr c0000082 core 2
core : 0x2 - msr[c0000082] = fffff807`73553180
```

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_DEBUGGER_READ_OR_WRITE_MSR`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_READ_AND_WRITE_ON_MSR {

  UINT64 Msr; // It's actually a 32-Bit value but let's not mess with a register
  UINT32 CoreNumber; // specifies the core to execute wrmsr or read the msr
                     // (DEBUGGER_READ_AND_WRITE_ON_MSR_APPLY_ALL_CORES mean all the cores)
  DEBUGGER_MSR_ACTION_TYPE
  ActionType; // Detects whether user needs wrmsr or rdmsr
  UINT64 Value;

} DEBUGGER_READ_AND_WRITE_ON_MSR, *PDEBUGGER_READ_AND_WRITE_ON_MSR;
```

Where `Msr` is `ecx` value for 'rdmsr' or 'wrmsr' instruction, `CoreNumber` is the target core that you want to read or write on it and `ActionType`  shows whether it's an 'rdmsr' or 'wrmsr'.

```c
typedef enum _DEBUGGER_MSR_ACTION_TYPE { DEBUGGER_MSR_READ, DEBUGGER_MSR_WRITE } DEBUGGER_MSR_ACTION_TYPE;
```

{% hint style="info" %}
If you want to execute '**wrmsr**' or '**rdmsr**', you should set CoreNumber to `DEBUGGER_READ_AND_WRITE_ON_MSR_APPLY_ALL_CORES`.
{% endhint %}

```c
#define DEBUGGER_READ_AND_WRITE_ON_MSR_APPLY_ALL_CORES 0xffffffff
```

### **Remarks**

This command will continue the debuggee for some time \(in Debugger Mode\). This means that you lose the current context \(registers & memory\) after executing this command.

### Requirements

None

### Related

[wrmsr \(write model-specific register\)](https://docs.hyperdbg.com/commands/debugging-commands/wrmsr)

