---
description: Description of the 'test' command in HyperDbg.
---

# test (test functionalities)

### Command

> test

### Syntax

> test \[Task (string)]

### Description

Tests the functionalities of HyperDbg in the running system.

### Parameters

**\[Task (string)]**

Different options are used mostly for debugging the internal mechanisms of HyperDbg.

<table><thead><tr><th>Option</th><th>Description</th><th data-hidden></th></tr></thead><tbody><tr><td><strong>query</strong></td><td>Shows the state of each core (e.g., halted or not)</td><td></td></tr><tr><td><strong>pool</strong></td><td>Shows the state of memory pool allocation and pre-allocation</td><td></td></tr><tr><td><strong>breakpoint</strong></td><td>Turns the interception of breakpoints (#BP) ON/OFF.</td><td></td></tr><tr><td><strong>trap</strong></td><td>Turns the interception of debug breaks (#DB) ON/OFF.</td><td></td></tr><tr><td><strong>trap-status</strong></td><td>Shows the state of simultaneously debugging threads that HyperDbg sets the trap flag on them</td><td></td></tr></tbody></table>

### Examples

The following command shows how to check all the test-cases.

```cpp
HyperDbg> test
```

The following command shows the '**lock**' state of each core.

```
0: kHyperDbg> test query
================================================ Debugging Lock Info ================================================
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1603) | Core : 0 is locked
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1603) | Core : 1 is locked
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1603) | Core : 2 is locked
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1603) | Core : 3 is locked
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1603) | Core : 4 is locked
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1603) | Core : 5 is locked

================================================ NMI Receiver State =======+=========================================
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1625) | Core : 0 - not called from an NMI handler (through the immediate VM-exit mechanism)
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1625) | Core : 1 - not called from an NMI handler (through the immediate VM-exit mechanism)
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1625) | Core : 2 - not called from an NMI handler (through the immediate VM-exit mechanism)
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1625) | Core : 3 - not called from an NMI handler (through the immediate VM-exit mechanism)
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1625) | Core : 4 - not called from an NMI handler (through the immediate VM-exit mechanism)
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (KdQuerySystemState:1625) | Core : 5 - not called from an NMI handler (through the immediate VM-exit mechanism)
```

The following command shows the state of pool allocation.

```
0: kHyperDbg> test pool
(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (PoolManagerShowPreAllocatedPools:218) | Pool details, Pool intention: 4 | Pool address: ffffc1059fdd1cc0 | Pool state: free | Should be freed: false | Already freed: false

(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (PoolManagerShowPreAllocatedPools:218) | Pool details, Pool intention: 4 | Pool address: ffffc1059fdd1f00 | Pool state: free | Should be freed: false | Already freed: false

(04:32:04.709 - core : 0 - vmx-root? yes)        [+] Information (PoolManagerShowPreAllocatedPools:218) | Pool details, Pool intention: 4 | Pool address: ffffc1059fdd1b40 | Pool state: free | Should be freed: false | Already freed: false
...
```

The following command is used to turn OFF/ON the breakpoint interception.

```
0: kHyperDbg> test breakpoint on
breakpoint interception is activated

0: kHyperDbg> test breakpoint off
breakpoint interception is deactivated
from now, the breakpoints will be re-injected into the guest debuggee
```

The following command shows the state of simultaneously debugging threads that HyperDbg sets the trap flag on them.

```
0: kHyperDbg> test trap-status
(01:14:17.385 - core : 0 - vmx-root? yes)        [+] Information (KdQueryRflagTrapState:1568) | Number of valid entries: 0x0
(Please be aware that only top 0x0 items are considered valid. There could be other items present in the array, but they are not valid.)
(01:14:17.385 - core : 0 - vmx-root? yes)        [+] Information (KdQueryRflagTrapState:1575) | g_TrapFlagState.ThreadInformation[0].ProcessId = 0 | ThreadId = 0
(01:14:17.385 - core : 0 - vmx-root? yes)        [+] Information (KdQueryRflagTrapState:1575) | g_TrapFlagState.ThreadInformation[1].ProcessId = 0 | ThreadId = 0
(01:14:17.385 - core : 0 - vmx-root? yes)        [+] Information (KdQueryRflagTrapState:1575) | g_TrapFlagState.ThreadInformation[2].ProcessId = 0 | ThreadId = 0
(01:14:17.385 - core : 0 - vmx-root? yes)        [+] Information (KdQueryRflagTrapState:1575) | g_TrapFlagState.ThreadInformation[3].ProcessId = 0 | ThreadId = 0
(01:14:17.385 - core : 0 - vmx-root? yes)        [+] Information (KdQueryRflagTrapState:1575) | g_TrapFlagState.ThreadInformation[4].ProcessId = 0 | ThreadId = 0
(01:14:17.385 - core : 0 - vmx-root? yes)        [+] Information (KdQueryRflagTrapState:1575) | g_TrapFlagState.ThreadInformation[5].ProcessId = 0 | ThreadId = 0
(01:14:17.385 - core : 0 - vmx-root? yes)        [+] Information (KdQueryRflagTrapState:1575) | g_TrapFlagState.ThreadInformation[6].ProcessId = 0 | ThreadId = 0
```

### IOCTL

None

### Remarks

Each time the connection to the debugger is restarted, the **breakpoint interception** and **debug break interception** are activated.

### Requirements

None

### Related

None
