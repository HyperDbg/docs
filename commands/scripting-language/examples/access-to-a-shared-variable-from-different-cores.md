# access to a shared variable from different cores

Sharing memory (variables) between different cores is tricky.

Imagine we want to count the number of times that `nt!NtCreateFile` system-calls are used in our system.

We know that the syscall number for `nt!NtCreateFile` is **0x55**.

In the [!syscall](https://docs.hyperdbg.org/commands/extension-commands/syscall#context) command, both `$context` and **RAX **register contain the syscall number.

One of the solutions to avoid simultaneous read/write from the shared objects (global variables) is using [spinlocks](https://en.wikipedia.org/wiki/Spinlock).

Spinlocks are implemented through functions in script engine.

{% hint style="warning" %}
Keep in mind, that the lock variable should be a global variable, not a local variable.
{% endhint %}

You can see the result of the above command:

![Counting NtCreateFile System-calls](../../../.gitbook/assets/counting-NtCreateFile-syscalls.PNG)

