---
description: Description of casting (type-awareness) and file (library) inclusion
---

# Casting & Inclusion

### Importing External Code

Starting from **HyperDbg v0.18**, the scripting language supports importing external script files using the `#include` directive. This feature allows users to split large scripts into multiple files, reuse common logic, and organize code in a more modular way.

The `#include` directive works similarly to the `#include` statement in C/C++ or the `import` keyword in Python. When a script is parsed, the contents of the included file are inserted into the current script before execution, or in other words, the `#include` directive behaves as if the contents of the specified file are appended to the top of the current script before execution.

#### Syntax

```c
#include "path/to/script.ds"
```

The path can be either **relative** or **absolute**, and the included file must be a valid HyperDbg script file.

#### Example: Including Multiple Script Files

In the following example, two external scripts are included. Functions defined in those files can then be used directly in the main script.

```c
? { 
    #include "script/test.ds";
    #include "script/fibonacci.ds";

    for (int i = 0; i < 3; i++) {
        my_func(1, 2);
    }

    var = myfibonacci(9);
    printf("%d", var);
}
```

#### Included Script Contents

In the previous examples, the included files contain function definitions that become available to the main script after inclusion.

For example, the `fibonacci.ds` file defines a recursive Fibonacci function:

```c
? {
    int myfibonacci(int var1) {
        if (var1 == 0) {
            return 0;
        }
        if (var1 == 1) {
            return 1;
        }
        return myfibonacci(var1 - 1) + myfibonacci(var1 - 2);
    }
}
```

Similarly, the `test.ds` file defines a simple function that prints its input arguments:

```c
? {
    void my_func(int var, int var2) {
        printf("var = %d, var2 = %d\n", var, var2);
        return;
        printf("this statement is never shown!\n");
    }
}
```

After including these files using the `#include` directive, the functions `myfibonacci` and `my_func` can be called directly from the main script as if they were defined in the same file.

In this case, the following results will be shown for the above example:

```
var = 1, var2 = 2
var = 1, var2 = 2
var = 1, var2 = 2
34
```

#### Example: Including a Script Using an Absolute Path

The `#include` directive also supports absolute paths:

```c
? {
    #include "C:\Users\xmaple555\CodeLibrary\HyperDbg\hyperdbg\hyperdbg-cli\test.ds";

    for (int i = 0; i < 3; i++) {
        my_func(1, 2);
    }
}
```

In this example, the result of the above script would be:

```
var = 1, var2 = 2
var = 1, var2 = 2
var = 1, var2 = 2
```

#### Notes

* Included scripts are processed **at parse time**, not at runtime.
* Functions, variables, and definitions from included files become available in the current script scope.

### Casting & type-awareness

At the moment, **dslang** does not support type-casting but the support for the types will be added to the script-engine in future versions!
