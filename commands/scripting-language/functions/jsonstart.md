---
description: Description of 'JsonStart' function in HyperDbg Scripts
---

# JsonStart

### Function

> JsonStart

### Syntax

> JsonStart\( \);

### Parameters

None

### Description

Start a **JSON** block.

### Examples

```text
JsonStart();
json("FirstArg", str(@rcx));
print("Name", dq(@rcx));
JsonEnd();
```

Print data as an ASCII string pointed by **rcx** register as a value whose name is `FirstArg`and print data as an 8-byte hex, pointed by **rcx** register as a value whose name is `Name`.

### **Remarks**

None

