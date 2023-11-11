---
description: Description about customizing HyperDbg builds
---

# Customize build

You have different options to build **HyperDbg** in the way you want.

## Configurations

Before building, you can change the following options in the **Configuration.h** file.

By default, **HyperDbg** sends the current time of the system with each message packet from kernel-mode to user-mode, if you set `ShowSystemTimeOnDebugMessages` to `FALSE` then it no longer sends date and time along with each message (These messages along with time are used in internal logging functions, and it's not exposed to the script engine).

```c
/**
 * @brief Configures whether to show the current system time in the output of
 * debug messages or not (only available on usermode tracing messages)
 *
 */
#define ShowSystemTimeOnDebugMessages TRUE
```

If you want to use **WPP Tracing** instead of **HyperDbg**'s message tracing, then set `UseWPPTracing` to `TRUE`. After that, you no longer see any message in **HyperDbg**, and instead, you can see the messages in a **WPP Tracing** compatible app.

```c
/**
 * @brief Use WPP Tracing instead of all logging functions
 *
 */
#define UseWPPTracing FALSE
```

If you set this option to `TRUE` then it uses **DbgPrint** instead of **HyperDbg**'s message tracing or **WPP Tracing**. Keep in mind that **DbgPrint** is not usable in most events as it's **not** vmx-root compatible.

```c
/**
 * @brief Configures whether to use DbgPrint or use the custom usermode message
 * tracking
 *
 */
#define UseDbgPrintInsteadOfUsermodeMessageTracking FALSE
```

Shows debug messages in both the user-mode app and debugger. It works only if you set `UseDbgPrintInsteadOfUsermodeMessageTracking` to `FALSE`

This option is **not** usable in the current version of **HyperDbg**.

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

This is one of the **important** options for **HyperDng**. By default, **HyperDbg** accumulates messages in a separate buffer, and it won't send them immediately to the user-mode buffers.

If you need to see messages immediately after each one message, then set this option to `TRUE`. However, it kills the performance as sending buffers to the user-mode involves various and heavy functions.

If you set this option to `FALSE` (**default**), then **HyperDbg** accumulates (**\~5** or more) messages, and when the buffer is full, it sends the buffer to the user-mode **CLI** or **GUI**.

```c
/**
 * @brief Use immediate messaging (means that it sends each message when they
 * recieved and do not accumulate them) it works only if you set
 * UseDbgPrintInsteadOfUsermodeMessageTracking to FALSE
 */
#define UseImmediateMessaging TRUE
```

The following option shows whether immediate messaging is active for each event or is not active. It means that if you set this option to `TRUE` then it's like to add `imm yes` to each event command, and if you set this option to `FALSE` then it's like to add `imm no` to each command.

You can still change a special event's behavior by specifying `imm yes` and `imm no`.

```c
/**
 * @brief Use immediate messaging (means that it sends each message when they
 * recieved and do not accumulate them) its the default value on events,
 * a user can change this behavior by selecting 'imm yes' or 'imm no' in the
 * case of events
 */
#define UseImmediateMessagingByDefaultOnEvents TRUE
```

The following option enables or disables debug-mode which determines whether the debuggee should send its pre-initialize logs to the debugger or not and also enters debugger in debugging section to break the debugger in the case of errors.

```c
/**
 * @brief Shows whether to show or not show the drivers debugging infomation
 * and also enters debugger in debugging section to break the debugger in the
 * case of errors
 */
#define DebugMode FALSE
```

The following option enables or disables the [instant event](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events) mechanism. If disabled, the debugger will continue the debuggee to apply events or [clear the events](https://docs.hyperdbg.org/commands/debugging-commands/events) in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode). Otherwise, events are applied immediately.

```c
/**
 * @brief Enable or disable the instant event mechanism
 * @details for more information: https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events
 *
 */
#define EnableInstantEventMechanism TRUE
```

## Definitions

Before building, you can change the following options in the **Definition.h** file.

The following option shows the **maximum** amount of packets that **HyperDbg** uses as storage for **unread** messages. For example, if you have a high rate of producing messages, you can increase the value. If the maximum capacity is full of **unread** messages, then **HyperDbg** overrides old messages, and you'll lose earlier messages.

```c
/* Default buffer size */
#define MaximumPacketsCapacity 1000 // number of packets
```

The following option shows the capacity of each packet in **HyperDbg** message tracing. If you have long messages or buffers, then you can increase this value.

```c
#define PacketChunkSize                                                        \
  1000 // NOTE : REMEMBER TO CHANGE IT IN USER-MODE APP TOO
```

**DbgPrint** has a size limitation. This option changes the default limitation of **DbgPrint**.

This option is **not** usable in the current version of **HyperDbg**.

```c
#define DbgPrintLimitation 512
```

The following option indicates the start seed for creating the event's `Tag` value.

```c
#define DebuggerEventTagStartSeed 0x1000000
```

The following option limits the count of maximum results for [s\*](https://docs.hyperdbg.org/commands/debugging-commands/s) and [!s\*](https://docs.hyperdbg.org/commands/extension-commands/s) commands.

```c
#define MaximumSearchResults 0x1000
```

The following option changes the **default TCP port** used to **listen** and **connect** to a remote system.

```c
#define DEFAULT_PORT "50000"
```

Determines how many sources a debugger can have for a single event.

```c
#define DebuggerOutputSourceMaximumRemoteSourceForSingleEvent 0x5
```

The seeds that user-mode codes use as the starter of their output source tag.

```c
#define DebuggerOutputSourceTagStartSeed 0x1
```

The following option changes the maximum number of breakpoints ('[bp](https://docs.hyperdbg.org/commands/debugging-commands/bp)' command) that the user can put into the entire system before continuing the debuggee. If you continue the debuggee and send an IOCTL, HyperDbg will reset this number.

```c
#define MAXIMUM_BREAKPOINTS_WITHOUT_CONTINUE 50
```

The following option changes the speed at which HyperDbg reads kernel messages in user-mode. It's the wait time before requesting any new request to read messages in milliseconds.

```c
/**
 * @brief The speed delay for showing messages from kernel-mode 
 * to user-mode in  VMI-mode, using a lower value causes the 
 * HyperDbg to show messages faster but you should keep in mind,
 *  not to eat all of the CPU
 */
#define DefaultSpeedOfReadingKernelMessages 30
```

