---
description: Description of HyperDbg Debugger Scripts
---

# Debugger Script (DS)

Batch scripts are essential parts of the debugger and the script engine, mainly used to automate debugging tasks.

Based on the complexity of developing different parts of the debugger and the fact that there are tens of [considerations](https://docs.hyperdbg.org/tips-and-tricks/considerations) for developing automated tasks in HyperDbg, we prefer to use the script engine to implement many features and commands. That's why HyperDbg came with a set of pre-defined [scripts](https://github.com/HyperDbg/scripts).&#x20;

You can write your scripts to automate your debugging routines without worrying about HyperDbg internals, as most limitations are checked through the script functions.

Generally, each functionality that is not called at a high rate and computation time is not a thing, is preferred to be implemented in Script Engine.

### Extensions

You can use plain text files for batch scripts, but as the convention, HyperDbg uses (**.ds**) extensions, the abbreviation of **D**ebugger **S**cript.

### Arguments

Arguments are passed to the scripts by using the `$arg0`, `$arg1`, `$arg2`, ..., `$arg100`, ..., `$arg1000` and so on.

The first argument (`$arg0`) is the script's **.ds** file path. Arguments can be both an expression, a constant, or a string. Constants are considered in **hex** format if no [prefix](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#number-prefixes) is specified.

### Running Scripts

There are two ways to run batch-script like commands in **HyperDbg**.

First, we can save our command(s) in a file and then use the [.script](https://docs.hyperdbg.org/commands/meta-commands/.script) command, and the second way is to pass the `--script` argument to the **hyperdbg-cli.exe**.

{% hint style="success" %}
In HyperDbg, both batch scripts and the script engine use a C-like [commenting style](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#comments).
{% endhint %}

For example, let's assume that we can use [.script](https://docs.hyperdbg.org/commands/meta-commands/.script) command like this:

```bash
HyperDbg> .script c:\users\sina\desktop\script.ds
```

If we want to pass arguments to the script.

```bash
HyperDbg> .script c:\users\sina\desktop\script.ds 0x55 @rax+@rbx 123
```

The second case is when we want to run our script from the command-line directly; then you can run the **hyperdbg-cli.exe** like this :

```bash
Microsoft Windows [Version 10.0.18362.900]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Users\sina\Desktop\HyperDbg>hyperdbg-cli.exe --script c:\users\sina\desktop\script.ds
```

If we need to pass arguments to the script.

```bash
Microsoft Windows [Version 10.0.18362.900]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Users\sina\Desktop\HyperDbg>hyperdbg-cli.exe --script c:\users\sina\desktop\script.ds 0x55 @rax+@rbx 123
```

With these two ways, we can automate our scripts in **HyperDbg**.

### Examples

Let's see some examples.

#### Example 1

Imagine we saved the following file at `C:\My HyperDbg Scripts\Hello World.ds"`. We want to pass two parameters, the first parameter (`$arg1`) as **0x55** and the second parameter (`$arg2`) as **@rax+@rbx**.

You can use the following command.

`.script "C:\My HyperDbg Scripts\Hello World.ds" 0x55 @rax+@rbx`

{% hint style="info" %}
Please note that you should put a space in the script file path between two quotations if there is a space in the script file path.
{% endhint %}

```clike
? {	
	printf("Hello World\n");
	printf("The first argument is : %llx\n", $arg1);
	printf("Result of rax + rbx is : %llx\n", $arg2);
}
```

The result would be:

```
Hello World
The first argument is : 55
Result of rax + rbx is : 5
```

#### Example 2

Imagine we want to intercept syscall for a particular process, get the Process ID and the Syscall Number from the user, and print the details for each execution of the target system call. We'll use the following script.

`.script "C:\My HyperDbg Scripts\Syscall Log.ds" 1ec0 0x55`

```clike
!syscall pid $arg1 script { 
	if (@rax == $arg2) {
			printf("[%llx:%llx] Syscall num: %llx, arg1: %llx, arg2: %llx, arg3: %llx, arg4: %llx\n", $pid, $tid, @rax, @rcx, @rdx, @r8, @r9);
	}
}
```

You can submit your scripts to the [**scripts**](https://github.com/HyperDbg/scripts) repo.
