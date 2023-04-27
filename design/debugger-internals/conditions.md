---
description: What is conditions in HyperDbg & how to use them?
---

# Conditions

### What is the condition?

Each event has a has condition buffer. If this buffer is null, then the **event is unconditional**, which means that each trigger of the event will result in the execution of all of the event's action(s).

If it's not null, then the **event is conditional**. In a **conditional event**, **actions** of that **event** will be executed only and only if the **condition** returns `TRUE`. Otherwise, all of the **actions** of that **event** will be ignored.

{% hint style="info" %}
Conditions apply to events, not actions. If you need multiple conditions for each action, you can create multiple events or use the custom code feature to check for the conditions.
{% endhint %}
