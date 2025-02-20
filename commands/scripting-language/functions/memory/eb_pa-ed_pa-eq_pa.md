---
description: Description of 'eb_pa', 'ed_pa', and 'eq_pa' functions in HyperDbg Scripts
---

# eb\_pa, ed\_pa, eq\_pa

#### Function

> eb\_pa

> ed\_pa

> eq\_pa

### Syntax

> eb\_pa( PhysicalAddress, Value );

> ed\_pa( PhysicalAddress, Value );

> eq\_pa( PhysicalAddress, Value );

### Parameters

**\[Expression (PhysicalAddress)]**

The physical address of the target memory that needs to be modified.

**\[Expression (Value)]**

The value that needs to be replaced on the target physical address.

### Description

`eb_pa` modifies a single `byte`.

`ed_pa` modifies a `dwrod`.

`eq_pa` modifies a `qword` value.

### Return value

If the address is valid, then the above functions return `1`. Otherwise, `0` is returned.

### Examples

The following code edits memory (**quad-word**) at `1d44fde0` and change it to `0x12345678deadbeef`.

`IsEditApplied = eq_pa(1d44fde0, 0x12345678deadbeef);`

The following code changes a **byte** to 0x90 at the location that the **rcx** register is pointing to, then adds **0x8** to it.

`IsEditApplied = eb_pa(poi(@rcx)+8, 0x90);`

You can also use these functions without checking for the return value.

`eq_pa(1d44fde0, 0x12345678deadbeef);`

It's possible to use these functions with an **if** statement.

```c
if (eq_pa(@r11, 0x12345678deadbeef) == 1) 
{ 
	printf("changes are applied.\n");
}
else 
{
	printf("changes are NOT applied.\n");
}
```

### Remarks

The support for these functions is added from **v0.13**.

### Related

[eb, ed, eq](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/eb-ed-eq)
