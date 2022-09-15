---
description: Description of 'event_disable' function in HyperDbg Scripts
---

# event\_disable

### Function

> event\_disable

### Syntax

> event\_disable( EventId );

### Parameters

**\[Expression (EventId)]**

Event ID of the event that needs to be deactivated.

### Description

Disables an event by its Event ID.

### Examples

`event_disable(1);`

Disable an event with `EventId = 1`.

### Remarks

You can see events' ID using the [event](https://docs.hyperdbg.org/commands/debugging-commands/events) command. The first column of [this command](https://docs.hyperdbg.org/commands/debugging-commands/events)'s results shows the event ID.

Starting from **v0.2**, this function is changed from `disable_event` to `event_disable`.

### Related

[event\_enable](https://docs.hyperdbg.org/commands/scripting-language/functions/events/event\_enable)

