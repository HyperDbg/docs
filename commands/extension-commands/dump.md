---
description: Description of '!dump' command in HyperDbg.
---

# !dump (save the physical memory into a file)

### Command

> !dump

### Syntax

> !dump \[FromAddress (hex)] \[ToAddress (hex)] \[path Path (string)]

### Description

Saves a range of the **physical** memory into a file.

### Parameters

**\[FromAddress (hex)]**

The start **physical** address of where it needs to be dumped.

**\[ToAddress (hex)]**

The end of the **physical** address of where it needs to be dumped.

**\[path Path (string)]**

The path of where the dump file needs to be saved.

### Examples

The following command saves the physical memory from the address `bd000` to `bf000` in the file  `c:\rev\dump1.dmp`.

```
HyperDbg> .dump bd000 bf000 path c:\rev\dump1.dmp
the dump file is saved at: c:\rev\dump1.dmp
```

The following command saves the physical memory from the address `bd000` to `bd000+6000` in the file  `c:\rev\dump2.dmp`.

```diff
HyperDbg> .dump bd000 bd000+6000 path c:\rev\dump2.dmp
the dump file is saved at: c:\rev\dump2.dmp
```

### IOCTL

This command reads the memory in the 4KB chunks and is the same as [this](https://docs.hyperdbg.org/commands/extension-commands/d#ioctl) command, just you have to set the memory reading `Style` to `DEBUGGER_SHOW_COMMAND_DUMP`.

### Remarks

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements

None

### Related

[.dump (save the virtual memory into a file)](https://docs.hyperdbg.org/commands/meta-commands/.dump)

[!db, !dc, !dd, !dq (read physical memory)](https://docs.hyperdbg.org/commands/extension-commands/d)
