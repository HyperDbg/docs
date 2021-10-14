# conditional breakpoints

Conditional statements are one of the important parts of debuggers and HyperDbg is not an exception.

These statements are handled in both command syntax like you can specify a **core **or **pid **that triggers the event but you can also use the script engine that gives you more flexibility to compare different arguments and if necessary, call functions.

In this example, we want to put a conditional breakpoint on `nt!ExAllocatePoolWithTag` only and only when it's called from `nt!CmpAllocatePoolWithTag` function.

Let's take a look at memory at bot
