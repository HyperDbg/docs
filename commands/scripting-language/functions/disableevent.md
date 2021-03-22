---
description: Description of 'DisableEvent' function in HyperDbg Scripts
---

# DisableEvent

### Function

> DisableEvent

### Syntax

> DisableEvent\( EventId \);

### Parameters

**\[EventId \(int\)\]**

         Event ID of the event that needs to be deactivated.

### Description

Disables an event by its Event ID.

### Examples

`DisableEvent(1);`

Disable an event with `EventId = 1`.

### **Remarks**

You can see events' IDs using the [event](https://docs.hyperdbg.com/commands/debugging-commands/events) command. The first column of [this command](https://docs.hyperdbg.com/commands/debugging-commands/events)'s results shows the event ID.

