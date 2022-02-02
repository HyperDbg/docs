---
description: Description of HyperDbg Batch Scripts
---

# HyperDbg Scripts (HDS)

Batch scripts are essential parts of the debugger and the script engine, mainly used to automate debugging tasks.

Based on the complexity of developing different parts of the debugger and the fact that there are tens of [considerations](https://docs.hyperdbg.org/tips-and-tricks/considerations) on developing automated tasks in HyperDbg, we prefer to use the script engine to implement many features and commands. That's why HyperDbg came with a set of pre-defined [scripts](https://github.com/HyperDbg/scripts).&#x20;

You can write your scripts to automate your debugging routines without worrying about HyperDbg internals, as most limitations are checked through the script functions.

Generally, each functionality that is not called at a high rate and computation time is not a thing, is preferred to be implemented in Script Engine.

### Extensions

You can use plain text files for batch scripts, but as the convention, HyperDbg uses (**.hds**) extensions, the abbreviation of **H**yper**D**bg **S**cripts.

### Arguments

Arguments Arguments are passed to the scripts by using the `$arg0`, `$arg1`, `$arg2`, ..., `$arg100`, ..., `$arg1000`, and so on.

The first argument (`$arg0`) is the script's **.hds** file path. Arguments can be both an expression, a constant, or a string. Constants are considered in **hex** format if no [prefix](https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations#number-prefixes) is specified.

### Examples

Let's see some examples.

#### Example 1

Imagine, we saved the following file at `C:\My HyperDbg Scripts\Hello World.hds"`. We want to pass two parameters, the first parameter (`$arg1`) as **0x55** and the second parameter (`$arg2`) as **@rax+@rbx**.

You can use the following command.

`.script "C:\My HyperDbg Scripts\Hello World.hds" 0x55 @rax+@rbx`

{% hint style="info" %}
Please note that you should put a space in the script file path between two quotations if there is a space in the script file path.
{% endhint %}

```clike
? {	
	printf("Hello World\n");
	printf("First argument is : %llx\n", $arg1);
	printf("Result of rax + rbx is : %llx\n", $arg2);
}
```

The result would be:

```
Hello World
First argument is : 55
Result of rax + rbx is : 5
```

#### Example 2

