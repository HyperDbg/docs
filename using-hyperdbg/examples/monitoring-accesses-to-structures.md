---
description: Finding the writers and reader of memory
---

# Monitoring Accesses To Structures

Imagine the process of a process is located at `0xffff83811f265040` and at current version of Windows, size of the `nt!_EPROCESS` is **0xa40**. We can conclude that this structure starts from `0xffff83811f265040` to `0xffff83811f265040 + 0xa40 = 0xffff83811f265a80`.

This way, if we want to break on any read/write to this structure, we use the following command.

```c
HyperDbg> !monitor rw 0xffff83811f265040 0xffff83811f265a80
```

Now, we want to create log from RIP\(s\) that try to write on nt!\_EPROCESS of that special process. We use the following command.

```c
HyperDbg> !monitor w 0xffff83811f265040 0xffff83811f265a80 script { print($ip); }
```

If we want to monitor RIP\(s\) for read \(not write\), then we use the following command.

```c
HyperDbg> !monitor w 0xffff83811f265040 0xffff83811f265a80 script { print($ip); }
```

