---
description: Description of 'test' command in HyperDbg.
---

# test \(test functionalities\)

### Command

> test

### Syntax

> test \[test-case number \(hex value\)\]

### Description

Tests the functionalities of HyperDbg in the running system.

### Parameters

\[test-case number \(hex value\)\] \(optional\)

          The number of a special test-case. If you don't specify this parameter then it will check all of the test-cases.

| Test-case Number | Description |
| :--- | :--- |
| 0x0 | Performs all the checks. |
| 0x1 | Test **!monitor** command |

### Examples

The following command shows how to check all the test-cases.

```cpp
HyperDbg> test
```

The following command shows the check for **0x2** test-case.

```c
HyperDbg> test 0x2
```

### IOCTL

None

### **Remarks**

None

### Requirements

None

### Related

None

