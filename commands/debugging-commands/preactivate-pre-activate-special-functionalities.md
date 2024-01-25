# preactivate (pre-activate special functionalities)

### Command

> preactivate

### Syntax

> preactivate \[Type (string)]

### Description

Initializes and pre-activates a specified functionality. This command is mainly used to optimize the pre-allocation of pools for [**instant events**](https://docs.hyperdbg.org/tips-and-tricks/misc/instant-events).

{% hint style="info" %}
This command is only used in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode). In the [VMI Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#vmi-mode), the initialization is done automatically.
{% endhint %}

### Parameters

**\[Type (string)]**

The type of functionality to be initialized.

| Type     |                                                                                                                     |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| **mode** | Used for initialization of the '[!mode](https://docs.hyperdbg.org/commands/extension-commands/mode)' event command. |

### Examples

If you use the '[!mode](https://docs.hyperdbg.org/commands/extension-commands/mode)' command in the [Debugger Mode](https://docs.hyperdbg.org/using-hyperdbg/prerequisites/operation-modes#debugger-mode), only for the first time, the following error will be shown.

```
0: kHyperDbg> !mode u pid 1c0 
err, the '!mode' event command cannot be directly initialized in the Debugger Mode. To avoid wasting system resources and performance issues we decided to use another command to initialize it first then use it. You can use the 'preactivate mode' command to preactivate this mechanism after that, you can use the '!mode' event (c000004e)
```

In order to solve this issue, the following command can be used:

```
0: kHyperDbg> preactivate mode
the requested service is activated successfully!
fffff807`9b9f8e62    0F 01 C1
```

Once you pre-activate the above functionality, it remains active until the next load of the debugger.

### IOCTL

This function works by calling **DeviceIoControl** with `IOCTL = IOCTL_PREACTIVATE_FUNCTIONALITY`, you have to send it in the following structure.

```c
typedef struct _DEBUGGER_PREACTIVATE_COMMAND
{
    DEBUGGER_PREACTIVATE_COMMAND_TYPE Type;
    UINT32                            KernelStatus;

} DEBUGGER_PREACTIVATE_COMMAND, *PDEBUGGER_PREACTIVATE_COMMAND;

```

You should only fill the **Type** of the above structure when the IOCTL returns from the kernel, other parts of this structure are filled with appropriate **KernelStatus**.

The **Type** can be from the following enum:

```
typedef enum _DEBUGGER_PREACTIVATE_COMMAND_TYPE
{
    DEBUGGER_PREACTIVATE_COMMAND_TYPE_MODE,

} DEBUGGER_PREACTIVATE_COMMAND_TYPE;
```

### Remarks

This command will continue the debuggee for some time (in Debugger Mode). This means that you lose the current context (registers & memory) after executing this command.

### Requirements

None

### Related

None
