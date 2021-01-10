---
description: Description of 'wrmsr' command in HyperDbg.
---

# wrmsr \(write model-specific register\)

### Command

> wrmsr

### Syntax

> wrmsr \[msr \(hex\) - ecx\] \[value \(hex\) - edx:eax\] core \[core number\(hex\)\]

### Description

Write on the model specific register using 'wrmsr' instruction.

### Parameters

\[msr \(hex\) - ecx\]

          The index of msr \(ECX Register for 'wrmsr' instruction\)

\[value \(hex\) - edx:eax\]

          The value to write on MSR \(edx:eax for 'wrmsr' instruction\)

core \[core number\(hex\)\] \(optional\)

          The core that we want to read the 'rdmsr' from

{% hint style="success" %}
If you don't specify the 'core' by default it will be applied to all the cores.
{% endhint %}

### Examples

The following command shows how we can change the MSR register `c0000082` to ``fffff807`73553180`` using `wrmsr` command.

```diff
HyperDbg> wrmsr c0000082 fffff807`73553180
```

The following command shows how we can change the MSR register `c0000082` to ``fffff807`73553180`` using `wrmsr` command for core `2`.

```diff
HyperDbg> wrmsr c0000082 fffff807`73553180 core 2
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
If you want to execute 'wrmsr' or 'rdmsr', you should set CoreNumber to DEBUGGER\_READ\_AND\_WRITE\_ON\_MSR\_APPLY\_ALL\_CORES.
{% endhint %}

```c
#define DEBUGGER_READ_AND_WRITE_ON_MSR_APPLY_ALL_CORES 0xffffffff
```

### **Remarks**

None

### Requirements

None

### Related

[rdmsr \(read model-specific register\)](https://docs.hyperdbg.com/commands/debugging-commands/rdmsr)

