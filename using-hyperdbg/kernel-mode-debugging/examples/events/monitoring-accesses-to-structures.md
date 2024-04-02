---
description: Finding the writers and reader of memory
---

# Monitoring Accesses To Structures

Have you ever tried to discover what functions read or write on a portion of memory?

**Hardware Debug Registers** have limitations, we only have four debug registers and these debug registers have a limitation on size (4, 2, 1).

Imagine the `nt!_EPROCESS` of a process is located at `0xffff83811f265040` and at the current version of Windows, the size of the `nt!_EPROCESS` is **0xa40**. We can conclude that this structure starts from `0xffff83811f265040` to `0xffff83811f265040 + 0xa40 = 0xffff83811f265a80`.

If we want to break on any read/write to this structure, we use the following command.

```c
HyperDbg> !monitor rw 0xffff83811f265040 0xffff83811f265a80
```

If we want to create a log from RIP(s) that tries to write on `nt!_EPROCESS` of that special process.

```c
HyperDbg> !monitor w 0xffff83811f265040 0xffff83811f265a80 script { print($ip); }
```

If we want to monitor RIP(s) for reading (not writing), we use the following command.

```c
HyperDbg> !monitor r 0xffff83811f265040 0xffff83811f265a80 script { print($ip); }
```
