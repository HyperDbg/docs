---
description: 'Enable, Disable, and remove events'
---

# Managing Events

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

