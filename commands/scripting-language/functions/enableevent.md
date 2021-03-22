---
description: Description of 'EnableEvent' function in HyperDbg Scripts
---

# EnableEvent

### Function

> EnableEvent

### Syntax

> EnableEvent\( EventId \);

### Parameters

**\[EventId \(int\)\]**

         Event ID of the event that needs to be activated.

### Description

Enables an event by its Event ID.

### Examples

`EnableEvent(1);`

Enable an event with `EventId = 1`.

### **Remarks**

You can see events' IDs using the [event](https://docs.hyperdbg.com/commands/debugging-commands/events) command. The first column of [this command](https://docs.hyperdbg.com/commands/debugging-commands/events)'s results shows the event ID.

