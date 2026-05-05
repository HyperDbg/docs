---
description: Description of '!lbr' command in HyperDbg.
---

# !lbr (enable, disable, and configure Last Branch Record)

### Command

> !lbr

### Syntax

> !lbr \[Function (string)]
>
> !lbr \[filter FilterOptions (string)]

### Description

Performs operations for Last Branch Record (LBR), including enabling, disabling, flushing, and configuring filters.

{% hint style="info" %}
To dump the LBR entries after enabling them, use the '[!lbrdump](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)' command.
{% endhint %}

### Parameters

**\[Function (string)]**

The target functionality. Can be one of the following values:

| Function | Description                              |
| -------- | ---------------------------------------- |
| enable   | Enable the Last Branch Record            |
| disable  | Disable the Last Branch Record           |
| flush    | Flush (clear) the Last Branch Record     |
| filter   | Configure the LBR filter options         |

**\[FilterOptions (string)] (optional)**

One or more filter options to configure which branch types are **not** captured. If no option is specified, everything is captured (default). Can be a combination of the following values:

| Option   | Description                          |
| -------- | ------------------------------------ |
| kernel   | Do not capture at ring 0             |
| user     | Do not capture at ring > 0           |
| jcc      | Do not capture conditional branches  |
| rel_call | Do not capture relative calls        |
| ind_call | Do not capture indirect calls        |
| return   | Do not capture near returns          |
| ind_jmp  | Do not capture indirect jumps        |
| rel_jmp  | Do not capture relative jumps        |
| far      | Do not capture far branches          |

### Examples

The following command enables the Last Branch Record.

```c
HyperDbg> !lbr enable
```

The following command disables the Last Branch Record.

```c
HyperDbg> !lbr disable
```

The following command flushes (clears) the Last Branch Record.

```c
HyperDbg> !lbr flush
```

The following command shows the current LBR filter configuration.

```c
HyperDbg> !lbr filter
```

The following command configures the LBR filter to not capture kernel-mode conditional branches, indirect jumps, relative jumps, and far branches.

```c
HyperDbg> !lbr filter kernel jcc ind_jmp rel_jmp far
```

The following command configures the LBR filter to not capture kernel-mode conditional branches, near returns, indirect jumps, relative jumps, and far branches.

```c
HyperDbg> !lbr filter kernel jcc return ind_jmp rel_jmp far
```

The following command configures the LBR filter to not capture kernel-mode conditional branches, indirect jumps, and relative jumps.

```c
HyperDbg> !lbr filter kernel jcc ind_jmp rel_jmp
```

The following command configures the LBR filter to not capture user-mode relative calls, indirect calls, near returns, and far branches.

```c
HyperDbg> !lbr filter user rel_call ind_call return far
```

### IOCTL

None

### Remarks

Starting from **v0.19**, this command was added to the HyperDbg debugger.

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

[!lbrdump (dump Last Branch Record entries)](https://docs.hyperdbg.org/commands/extension-commands/lbrdump)
