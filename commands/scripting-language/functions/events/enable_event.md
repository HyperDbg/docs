---
description: Description of the 'event_enable' function in HyperDbg Scripts
---

# event\_enable

### Function

> event\_enable

### Syntax

> event\_enable( EventId );

### Parameters

**\[ Expression (EventId)]**

Event ID of the event that needs to be activated.

### Description

Enables an event by its Event ID.

### Examples

`event_enable(1);`

Enable an event with `EventId = 1`.

When an event is executed, the `$event_id` pseudo-register contains the target event's ID.

### Remarks

You can see events' ID using the [event](https://docs.hyperdbg.org/commands/debugging-commands/events) command. The first column of [this command](https://docs.hyperdbg.org/commands/debugging-commands/events)'s results shows the event ID.

Starting from **v0.2**, this function is changed from `enable_event` to `event_enable`.

### Related

[event\_disable](https://docs.hyperdbg.org/commands/scripting-language/functions/events/event\_disable)

[event\_clear](https://docs.hyperdbg.org/commands/scripting-language/functions/events/event\_clear)
