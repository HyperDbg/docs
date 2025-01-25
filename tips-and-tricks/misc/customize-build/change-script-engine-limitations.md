---
description: Changing the execution constants of the script engine
---

# Change Script Engine Limitations

If HyperDng displays an error message such as "**err, exceeding the max execution count**" indicating that the maximum number of script engine executions has been reached, you can resolve this issue by increasing the execution limit.

To fix this problem, you must build a customized version of HyperDbg that uses the most suitable number for your requirements.

Navigate to the source code and locate the following macro. In the header files, search for `MAX_EXECUTION_COUNT` and you will see the following code:

```clike
/**
 * @brief Maximum number of variables that can be used in the script engine
 */
#define MAX_EXECUTION_COUNT 1000000
```

The above macro defines the maximum number of operations that are possible to run in a single round of script execution. You must increase it according to your estimation.&#x20;

If HyperDng displays an error message such as "**err, stack buffer overflow**" indicating that the maximum number of stack variables has been pushed and the stack is full, you can resolve this issue by increasing the execution limit.

Navigate to the source code and locate the following macro. In the header files, search for `MAX_STACK_BUFFER_COUNT` and you will see the following code:

```c
/**
 * @brief Maximum number of stack buffer count in the script engine
 */
#define MAX_STACK_BUFFER_COUNT 256
```

The above macro defines the maximum variables that could be pushed into the stack. You must increase it according to your estimation.&#x20;

After that, [rebuild](https://docs.hyperdbg.org/getting-started/build-and-install) HyperDbg, and use your custom-made version of the debugger.
