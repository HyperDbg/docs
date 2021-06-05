---
description: 'Description of ''eb'', ''ed'', and ''eq'' functions in HyperDbg Scripts'
---

# eb, ed, eq

### Function

> eb

> ed

> eq

### Syntax

> eb\(Address, Value\);

> ed\(Address, Value\);

> eq\(Address, Value\);

### Parameters

**\[Address \(Expression\)\]**

         Address of the target memory that needs to be modified.

**\[Value \(Expression\)\]**

         The value that needs to be replaced on the target address.

### Description

`eb` modifies a single `byte`.

 `ed` modifies a `dwrod` .

`eq` modifies a `qword` value.

### Return

If the address is valid, then the above functions return `1`. Otherwise, `0` is returned.

### Examples

The following code edits memory \(**quad-word**\) at `fffff8031d44fde0` and change it to `0x12345678deadbeef`.

`IsEditApplied = eq(fffff8031d44fde0, 0x12345678deadbeef);`

The following code changes a **byte** to 0x90 at the location that the **@rcx** register is pointing to, then adds **0x8** to it.

`IsEditApplied = eb(poi(@rcx)+8, 0x90);`

### **Remarks**

None

