---
description: Description of the 'ucpuid' command in HyperDbg.
---

# ucpuid (execute CPUID instruction in debuggee)

### Command

> ucpuid

### Syntax

> ucpuid \[Function (hex)] \[SubFunction (hex)]

### Description

Executes the **CPUID** instruction on the target debuggee and displays the structured results.

{% hint style="info" %}
This command executes CPUID in the **target debuggee** (not the debugger). In local debugging mode, the debuggee and the debugger are the same machine, so the results reflect the local processor.
{% endhint %}

{% hint style="success" %}
Starting from **HyperDbg v0.23**, this command is available.
{% endhint %}

### Parameters

**\[Function (hex)]**

The CPUID leaf (i.e., the value placed in `EAX` before executing the `CPUID` instruction).

**\[SubFunction (hex)] (optional)**

The CPUID sub-leaf (i.e., the value placed in `ECX` before executing the `CPUID` instruction). If not specified, defaults to `0`.

{% hint style="info" %}
Use `ucpuid 0` to see the maximum supported basic CPUID leaf.

Use `ucpuid 0x80000000` to see the maximum supported extended CPUID leaf.
{% endhint %}

### Examples

The following example shows the result of `CPUID` with `EAX=0` (vendor and maximum basic leaf).

```diff
1: kHyperDbg> ucpuid 0
  *******************************************************
  *               LEAF 0 HAS NO SUBLEAVES               *
  *       ANY SUBLEAF YOU ENTER WILL DEFAULT TO 0       *
  *      AND THE PROCESSOR RETURNS UNDEFINED VALUES     *
  *******************************************************

  Vendor : GenuineIntel
  Maximum supported basic leaf : 22
```

The following example shows the result of `CPUID` with `EAX=0x80000000` (maximum supported extended leaf).

```diff
1: kHyperDbg> ucpuid 0x80000000
==== CPUID.(EAX=80000000H) Extended Function Information ====

  *******************************************************
  *           LEAF 0x80000000 HAS NO SUBLEAVES          *
  *       ANY SUBLEAF YOU ENTER WILL DEFAULT TO 0       *
  *      AND THE PROCESSOR RETURNS UNDEFINED VALUES     *
  *******************************************************

-- EAX --

  MaxExtendedFunctions = 0x80000008 (2147483656)

-- EBX --

  Reserved = 0x00000000

-- ECX --

  Reserved = 0x00000000

-- EDX --

  Reserved = 0x00000000
```

The following example shows the result of `CPUID` with `EAX=1` (version and feature information).

```diff
1: kHyperDbg> ucpuid 0x1
==== CPUID.(EAX=01H) Version / Additional / Feature Information ====

  *******************************************************
  *               LEAF 1 HAS NO SUBLEAVES               *
  *       ANY SUBLEAF YOU ENTER WILL DEFAULT TO 0       *
  *      AND THE PROCESSOR RETURNS UNDEFINED VALUES     *
  *******************************************************

-- EAX: Version Information --

  SteppingId       = 2
  Model            = 5
  FamilyId         = 6
  ProcessorType    = 0
  ExtendedModelId  = 10
  ExtendedFamilyId = 0

-- EBX: Additional Information --

  BrandIndex        = 0
  ClflushLineSize   = 8 (cache line = 64 bytes)
  MaxAddressableIds = 8
  InitialApicId     = 1

-- ECX: Feature Information --

  SSE3                  = TRUE
  PCLMULQDQ             = TRUE
  DTES64                = FALSE
  MONITOR/MWAIT         = FALSE
  CPL Qualified DS      = FALSE
  VMX                   = TRUE
  SMX                   = FALSE
  EIST (SpeedStep)      = FALSE
  TM2                   = FALSE
  SSSE3                 = TRUE
  L1 Context ID         = FALSE
  Silicon Debug         = FALSE
  FMA                   = TRUE
  CMPXCHG16B            = TRUE
  xTPR Update Control   = FALSE
  PDCM                  = FALSE
  PCID                  = TRUE
  DCA                   = FALSE
  SSE4.1                = TRUE
  SSE4.2                = TRUE
  x2APIC                = TRUE
  MOVBE                 = TRUE
  POPCNT                = TRUE
  TSC-Deadline          = TRUE
  AESNI                 = TRUE
  XSAVE/XRSTOR          = TRUE
  OSXSAVE               = TRUE
  AVX                   = TRUE
  F16C                  = TRUE
  RDRAND                = TRUE

-- EDX: Feature Information --

  FPU                   = TRUE
  VME                   = TRUE
  DE                    = TRUE
  PSE                   = TRUE
  TSC                   = TRUE
  MSR (RDMSR/WRMSR)     = TRUE
  PAE                   = TRUE
  MCE                   = TRUE
  CX8 (CMPXCHG8B)       = TRUE
  APIC On-Chip          = TRUE
  SEP (SYSENTER/EXIT)   = TRUE
  MTRR                  = TRUE
  PGE                   = TRUE
  MCA                   = TRUE
  CMOV                  = TRUE
  PAT                   = TRUE
  PSE-36                = TRUE
  PSN                   = FALSE
  CLFSH                 = TRUE
  DS (Debug Store)      = FALSE
  ACPI (Thermal/Clock)  = FALSE
  MMX                   = TRUE
  FXSR (FXSAVE/FXRSTOR) = TRUE
  SSE                   = TRUE
  SSE2                  = TRUE
  SS (Self Snoop)       = TRUE
  HTT                   = TRUE
  TM (Thermal Monitor)  = FALSE
  PBE                   = FALSE
```

The following example uses a sub-leaf. For example, `CPUID` leaf `4` with sub-leaf `2`.

```diff
1: kHyperDbg> ucpuid 4 2
```

### SDK

To execute the CPUID instruction in the target debuggee, use the following function in `libhyperdbg`:

```c
BOOLEAN
hyperdbg_u_request_cpuid(UINT32 FunctionId, UINT32 SubFunctionId);
```

### Remarks

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[cpu (check cpu supported technologies)](https://docs.hyperdbg.org/commands/debugging-commands/cpu)

[!cpuid (hook CPUID instruction execution)](https://docs.hyperdbg.org/commands/extension-commands/cpuid)

