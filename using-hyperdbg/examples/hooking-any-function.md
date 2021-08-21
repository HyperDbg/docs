---
description: Description about hooking options in HyperDbg
---

# Hooking Any Function

Hooking is a powerful feature of HyperDbg. You can hook all user-mode and kernel-mode functions and expect a fast in-line hook or unlimited EPT hooks.

Currently, hooking functions is possible through the [!epthook](https://docs.hyperdbg.com/commands/extension-commands/epthook) and [!epthook2](https://docs.hyperdbg.com/commands/extension-commands/epthook2).

**!epthook2** is generally a faster hook; however, it has some limitations that are mentioned [here](https://docs.hyperdbg.com/commands/extension-commands/epthook2#remarks).

Assume that `ExAllocatePoolWithTag` is located at ``fffff805`5cdb2030``.

As you can see on [MSDN](https://docs.microsoft.com/en-us/windows-hardware/drivers/ddi/wdm/nf-wdm-exallocatepoolwithtag), this function is defined like this :

```c
PVOID ExAllocatePoolWithTag(
  __drv_strictTypeMatch(__drv_typeExpr)POOL_TYPE PoolType,
  SIZE_T                                         NumberOfBytes,
  ULONG                                          Tag
);
```

Another thing is that the above function is called with **Fastcall** calling convention, and the parameters are passed in the following order `rcx`, `rdx`, `r8`, `r9`, and the rest of them are located on the stack. So, we have two options here to create a log from the parameters of this function. For example, we want to create a log from the **Tags** which is on `r8`.

```c
HyperDbg> !epthook fffff805`5cdb2030 script { print(@r8); }
```

If we want to use **!epthook2**, then the following command is used :

```c
HyperDbg> !epthook2 fffff805`5cdb2030 script { print(@r8); }
```

