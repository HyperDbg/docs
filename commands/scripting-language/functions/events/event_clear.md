---
description: Description of 'event_clear' function in HyperDbg Scripts
---

# event\_clear

### Function

> event\_clear

### Syntax

> event\_clear( EventId );

### Parameters

**\[Expression (EventId)]**

Event ID of the event that needs to be deactivated.

### Description

Clears an event by its Event ID.

### Examples

`event_clear(1);`

Clear an event with `EventId = 1`.

When an event is executed, the `$id` pseudo-register contains the target event's ID.

For example, you can force an event to be cleared immediately:

```
!epthook nt!ExAllocatePoolWithTag script {
 event_clear($id);
}
```

The above example, won't guarantee that only one event will be executed, as multiple cores might reach to the above code simultaneously. If you want to avoid multiple runs, you can use [interlocked](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked) functions.

### Remarks

You can see events' ID using the [event](https://docs.hyperdbg.org/commands/debugging-commands/events) command. The first column of [this command](https://docs.hyperdbg.org/commands/debugging-commands/events)'s results shows the event ID.

Starting from **v0.7**, this function is added to HyperDbg as a result of implementing the [instant event](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events) mechanism.

### Related

[event\_enable](https://docs.hyperdbg.org/commands/scripting-language/functions/events/event\_enable)

[event\_disable](https://docs.hyperdbg.org/commands/scripting-language/functions/events/event\_disable)
