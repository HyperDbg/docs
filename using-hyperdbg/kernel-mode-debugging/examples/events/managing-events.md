---
description: 'Enable, Disable, and remove events'
---

# Managing Events

Each time you use a command like [!syscall](https://docs.hyperdbg.org/commands/extension-commands/syscall), [!epthook](https://docs.hyperdbg.org/commands/extension-commands/epthook), [bp](https://docs.hyperdbg.org/commands/debugging-commands/bp), [!monitor](https://docs.hyperdbg.org/commands/extension-commands/monitor), etc., you create an event.

After that, you can disable or re-enable the event or completely clear the event. This article demonstrates how to manage events in HyperDbg.

The following command shows the list of active/disabled events.

```diff
HyperDbg> events
0       (enabled)           !syscall 80
1       (disabled)          !sysret 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command disables an event with event number `1` and then we see the list of all events.

```diff
HyperDbg> event d 1

HyperDbg> events
0       (enabled)           !syscall 80
1       (disabled)          !sysret 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command enables all of the events and commands.

```text
HyperDbg> event e all

HyperDbg> events
0       (enabled)           !syscall 80
1       (enabled)           !sysret 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command clears an event with event number `1`.

```text
HyperDbg> event c 1

HyperDbg> events
0       (enabled)           !syscall 80
2       (enabled)           !msrwrite 80 code {90}
3       (enabled)           !cpuid
```

The following command clears and turns off every enabled and disabled event and commands.

```text
HyperDbg> event c 1
```

