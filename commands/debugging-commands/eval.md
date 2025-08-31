---
description: Description of the '?' command in HyperDbg.
---

# ? (evaluate and execute expressions and scripts in debuggee)

### Command <a href="#command" id="command"></a>

> ?

### Syntax <a href="#syntax" id="syntax"></a>

> ? \[Expression (string)]

### Description <a href="#description" id="description"></a>

Evaluates and executes an expression in the remote debuggee or in the user debugger.

{% hint style="success" %}
You can write multiline scripts by putting the scripts between two curly brackets.

For example,&#x20;

`? {`&#x20;

&#x20;    `printf("first line");`

`printf("second line");`

`printf("third line");`

`}`
{% endhint %}

### Parameters <a href="#parameters" id="parameters"></a>

**\[Expression (string)]**

The expression is based on HyperDbg's [scripting language](https://web.archive.org/web/20210228003007/https://docs.hyperdbg.org/commands/scripting-language/assumptions-and-evaluations).

### Examples <a href="#examples" id="examples"></a>

The following command shows the `@rax` register in the debuggee by calling the [print](https://web.archive.org/web/20210228003007/https://docs.hyperdbg.org/commands/scripting-language/functions/print) function.

```clike
0: kHyperDbg> ? print(@rax);
```

You can use register assignment within scripts (You can change the value of registers).

```clike
0: kHyperDbg> ? @rax = 0x55;	        // change the RAX register value

0: kHyperDbg> ? @cr0 = @cr0 | 0x4;        // set the third bit of CR0 register

0: kHyperDbg> ? @zf = 1;   	        // set the Zero Flag

0: kHyperDbg> ? @ebx = @ecx + 10 + @ax;   // set the value of EBX register
```

HyperDbg supports [conditional statements](https://docs.hyperdbg.org/commands/scripting-language/conditionals-and-loops) like **if**, **elsif**, and **else**. You can use any expressions, registers, pseudo-registers, variables, or results of functions in the **if**, **elsif** statements.

```clike
0: kHyperDbg> ? {
    if (@rax == 55) {
    	     printf("rax is equal to 0x55");
    }
}

0: kHyperDbg> ? {
    if (poi(@rcx + 0x10) == ffff7080deadbeef && @rdx != 55 || $pid == 4) {
    	     printf("condition is met\n");
    }
}

0: kHyperDbg> ? {
    if (check_address(@r11) == 1) { 
    	     printf("address is valid.\n");
    }
    else
    {
	     printf("address is invalid.\n");
    }
}
```

For loops and nested loops are supported.

```clike
0: kHyperDbg> ? {
for (i = 0; i < 10 ; i++) {
       for (j = 0; j < 10; j++) {
       	  printf("%d, %d\n", i, j); 
       }
    }
 }
```

If you want to change memory, you can use [eq, ed, eb](https://docs.hyperdbg.org/commands/scripting-language/functions/memory/eb-ed-eq) functions.

```css
0: kHyperDbg> ? {

	if (eq(@r11, 0x12345678deadbeef) == 1) { 
	    printf("changes are applied.\n");
	}
	else {
            printf("changes are NOT applied.\n");
	}
}
```

There are many functions and examples available for HyperDbg’s script engine [here](https://docs.hyperdbg.org/commands/scripting-language/functions).

### Arguments <a href="#examples" id="examples"></a>

Arguments to the script are passed to the script by using the `$arg0`, `$arg1`, `$arg2`, ..., `$arg100`, ..., `$arg1000` and so on. HyperDbg uses the ‘**.ds**’ extension for the script files.

The first argument (`$arg0`) is the script's **.ds** file path. Arguments can be both an expression, a constant, or a string. Constants are considered in hex format if no prefix is specified.

#### Example

```clike
? { 
      printf("First argument is : %llx\n", $arg1);
      printf("Result of rax + rbx is : %llx\n", $arg2);
}
```

We run the above script like by using the “[.script](https://docs.hyperdbg.org/commands/meta-commands/.script)” command.

```clike
0: kHyperDbg> .script "C:\path to script\script.ds" 55 @rax+@rbx
```

### IOCTL <a href="#ioctl" id="ioctl"></a>

To run a script on the target process (thread) in the user debugger or in the target debuggee in the kernel debugger, you need to use the following function in `libhyperdbg`:

```clike
BOOLEAN
hyperdbg_u_run_script(CHAR * Expr, BOOLEAN ShowErrorMessageIfAny);
```

### Remarks <a href="#remarks" id="remarks"></a>

Other aliases for this command are '**eval**' and '**evaluate**'.

This command is guaranteed to keep debuggee in a halt state (in Debugger Mode); thus, nothing will change during its execution.

### Requirements <a href="#requirements" id="requirements"></a>

None

### Related <a href="#related" id="related"></a>

None
