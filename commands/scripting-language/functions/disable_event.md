---
description: Description of 'disable_event' function in HyperDbg Scripts
---

# disable\_event

### Function

> disable\_event

### Syntax

> disable\_event\( EventId \);

### Parameters

**\[EventId \(int\)\]**

         Event ID of the event that needs to be deactivated.

### Description

Disables an event by its Event ID.

### Examples

`disable_event(1);`

Disable an event with `EventId = 1`.

### **Remarks**

You can see events' ID using the [event](https://docs.hyperdbg.com/commands/debugging-commands/events) command. The first column of [this command](https://docs.hyperdbg.com/commands/debugging-commands/events)'s results shows the event ID.

