# access to a shared variable from different cores

Sharing memory (variables) between different cores is tricky.

Imagine we want to count the number of times that `nt!NtCreateFile` system-calls are used in our system.

We know that the syscall number for `nt!NtCreateFile` is **0x55**.

In the [!syscall](https://docs.hyperdbg.org/commands/extension-commands/syscall#context) command, both `$context` and **RAX **register contain the syscall number.

Th



You can see the result of the above command:

![Counting NtCreateFile System-calls](../../../.gitbook/assets/counting-NtCreateFile-syscalls.PNG)

