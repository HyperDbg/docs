---
description: Description about customizing HyperDbg builds
---

# Customize Build

You have different options to build HyperDbg in a way you want.

### Configurations

Before building, you can change the following options in **Configuration.h** file.

```c
/**
 * @brief Configures whether to show the current system time in the output of
 * debug messages or not (only available on usermode tracing messages)
 *
 */
#define ShowSystemTimeOnDebugMessages TRUE
```

```c
/**
 * @brief Use WPP Tracing instead of all logging functions
 *
 */
#define UseWPPTracing FALSE

```

```c
/**
 * @brief Configures whether to use DbgPrint or use the custom usermode message
 * tracking
 *
 */
#define UseDbgPrintInsteadOfUsermodeMessageTracking FALSE

```

```c
/**
 * @brief Show debug messages in both usermode app and debugger,
 * it works only if you set UseDbgPrintInsteadOfUsermodeMessageTracking to FALSE
 *
 */

//
// Should be FALSE, I realized that if we enable this flag, we end up in a
// situation that DbgPrint halts the system because it is executing in
// Dispatch-level in a DPC routine, I left it to FALSE for future attention
//
#define ShowMessagesOnDebugger FALSE
```

```c
/**
 * @brief Use immediate messaging (means that it sends each message when they
 * recieved and do not accumulate them) it works only if you set
 * UseDbgPrintInsteadOfUsermodeMessageTracking to FALSE
 */
#define UseImmediateMessaging FALSE
```

### Definitions

Before building, you can change the following options in **Configuration.h** file.

```c
/* Default buffer size */
#define MaximumPacketsCapacity 1000 // number of packets
```

```c
#define PacketChunkSize                                                        \
  1000 // NOTE : REMEMBER TO CHANGE IT IN USER-MODE APP TOO
```

```c
#define DbgPrintLimitation 512
```

```c
#define DebuggerEventTagStartSeed 0x1000000
```

```c
#define MaximumSearchResults 0x1000
```

