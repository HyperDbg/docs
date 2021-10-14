# access to a shared variable from different cores

Sharing memory (variables) between different cores is tricky.

Imagine we want to count the number of times that `nt!NtCreateFile` system-calls are used in our system.

We know that the syscall number for `nt!NtCreateFile` is **0x55**.

In the [!syscall](https://docs.hyperdbg.org/commands/extension-commands/syscall#context) command, both `$context` and **RAX **register contain the syscall number.

One of the solutions to avoid simultaneous read/write from the shared objects (global variables) is using [spinlocks](https://en.wikipedia.org/wiki/Spinlock).

[Spinlocks](https://docs.hyperdbg.org/commands/scripting-language/functions/spinlocks) are implemented through functions in the script engine.

First of all, we should define two global variables. One to be used as the counter and the other is used as the [lock](https://en.wikipedia.org/wiki/Lock_\(computer_science\)).

The can be achieved by using the following commands.

```
? .my_lock = 0;
? .my_counter = 0;
```

{% hint style="warning" %}
Keep in mind, that the lock variable should be a global variable, not a local variable.
{% endhint %}

Now, we will 

```
spinlock_lock(&.my_lock); 

.my_counter = .my_counter + 1;
printf("NtCreateFile syscall (0x0055) is called %llx times\n", .my_counter);
	
spinlock_unlock(&.my_lock);
```

{% hint style="danger" %}
If you don't use spinlocks and access to the global variables without using a lock then your result might not be true as the debuggee might run the script simultaneously in two cores and 
{% endhint %}

You can see the result of the above command:

![Counting NtCreateFile System-calls](../../../.gitbook/assets/counting-NtCreateFile-syscalls.PNG)

