---
description: Description of 'disable_event' function in HyperDbg Scripts
---

# disable\_event

### Function

> disable\_event

### Syntax

> disable\_event\( EventId \);

### Parameters

**\[Expression \(EventId\)\]**

Event ID of the event that needs to be deactivated.

### Description

Disables an event by its Event ID.

### Examples

`disable_event(1);`

Disable an event with `EventId = 1`.

### Remarks

You can see events' ID using the [event](https://docs.hyperdbg.org/commands/debugging-commands/events) command. The first column of [this command](https://docs.hyperdbg.org/commands/debugging-commands/events)'s results shows the event ID.

### Related

[enable\_event](https://docs.hyperdbg.org/commands/scripting-language/functions/events/enable_event)

