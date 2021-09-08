---
description: Description of 'enable_event' function in HyperDbg Scripts
---

# enable\_event

### Function

> enable\_event

### Syntax

> enable\_event\( EventId \);

### Parameters

**\[EventId \(int\)\]**

         Event ID of the event that needs to be activated.

### Description

Enables an event by its Event ID.

### Examples

`enable_event(1);`

Enable an event with `EventId = 1`.

### **Remarks**

You can see events' ID using the [event](https://docs.hyperdbg.com/commands/debugging-commands/events) command. The first column of [this command](https://docs.hyperdbg.com/commands/debugging-commands/events)'s results shows the event ID.

### Related

[disable\_event](https://docs.hyperdbg.com/commands/scripting-language/functions/events/disable_event)

