# count occurrences of an event

One of the use cases of HyperDbg is counting the occurrence of different events. However, incrementing and decrementing values from different cores is problematic as you can't add everything using mathematical operators. It is because the result of the computation might not be accurate.

The problem arises because if you want to access a value to add or subtract the value, other cores might do the same operation simultaneously. Each of the cores is computing the new values at the same time, so we might (and will) lose some of the operations as other core's results might replace the current core's computation re.

In order to prevent this problem, you can use interlocked functions in HyperDbg's script engine.

Imagine we want to count the number of times that system process (process id = 4) will access a paged-out page and leads to a page-fault.

For this purpose, first, we define a global variable.

```
? .my_counter = 0;
```

After that, we'll use the [!exception](https://docs.hyperdbg.org/commands/extension-commands/exception) command with the **0xe** parameter that shows we want to intercept page-faults and **pid 4**, which shows that only the system process should trigger the event.

In the event's script, we use the [interlocked_increment](https://docs.hyperdbg.org/commands/scripting-language/functions/interlocked/interlocked_increment) function to each time increment the value by **1**. 

```
!exception 0xe pid 4 script {
	
	.Result = interlocked_increment(&.my_counter);
}
```

Now we let the debuggee continue its normal execution using the '[g](https://docs.hyperdbg.org/commands/debugging-commands/g)' command.

After some time, we can read the value of the `.my_counter` global variable using the '[print](https://docs.hyperdbg.org/commands/debugging-commands/print)' command.

```
print .my_counter
```

You can see the final results:

![Get count of page-faults](broken-reference)

