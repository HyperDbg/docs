---
description: Description of constants and functions
---

# Constants & Functions

Starting from **v0.8.2**, HyperDbg supports functions in the script engine. Here's a description of how function can be defined within scripts.

## Naming convention

We suggest using [**snake\_case**](https://en.wikipedia.org/wiki/Snake_case) as the [naming convention](https://en.wikipedia.org/wiki/Naming_convention_\(programming\)). However, you can use any other naming conventions as well.

## Functions

HyperDbg functions are defined using the same syntax as the C programming language.

### Function: void

This is an example of a function that does not return any value (void). This code defines `my_func` which prints two integers. It's called three times with arguments (**1**, **2**). Then, it prints the hexadecimal value of variable `my_var`, which is `79`.

```c
? {
    
  void my_func(int var, int var2) {
    printf("var = %d, var2 = %d\n", var, var2);
    return;
    printf("this statement is never shown!\n");
  }
  
  for (int i = 0; i < 3; i++) {
    my_func(1, 2);
  }

  int my_var = 79;
  printf("%x\n", my_var);
}
```

### Function: int

This is an example of functions that return an integer. This code defines four functions: `my_func1`, `my_func2`, `my_func3`, and `my_func4`.

`my_func1` adds **1** to its argument and prints the result.

`my_func2` multiplies its argument by **2** and prints the result.

`my_func3` also multiplies its argument by **2** and prints the result.

`my_func4` calls `my_func1`, `my_func2`, and `my_func3`, then sums their results and prints the total.&#x20;

Finally, it calls `my_func4` with an argument of **2** and prints the result.

```c
? {
    
  int my_func1(int var1) {
    result = var1 + 1;
    printf("my_func1 %d\n", result);
    return result;
  }
  
  int my_func2(int var1) {
    result = var1 * 2;
    printf("my_func2 %d\n", result);
    return result;
  }
  
  int my_func3(int var1) {
    result = var1 * 2;
    printf("my_func3 %d\n", result);
    return result;
  }
  
  int my_func4(int var1) {
    result = my_func1(var1) + my_func2(var1) + my_func3(var1);
    printf("my_func4 %d\n", result);
    return result;
  }

  printf("%d\n", my_func4(2));
}
```

### Examples

#### Example 1: Recursive Fibonacci

The following function calculates the nth Fibonacci number recursively.

```c
? {

  int my_func(int var1) {
    if (var1 == 0) {
      return 0;
    }
    if (var1 == 1) {
      return 1;
    }
    return my_func(var1 - 1) + my_func(var1 - 2);
  }
  
  var = my_func(9);
  printf("%d", var);
}
```

#### Example 2: Iterative Fibonacci

This function calculates the nth Fibonacci number iteratively using a loop.

```c
? {

  int my_func(int varn) {
      
    int varf0 = 0;
    int varf1 = 1;
    int varfn = 0; //comment

    if (varn == 0) {
      return varf0;
    }
    if (varn == 1) {
      return varf1;
    }

    int i = 0;
    
    for (i = 2; i <= varn; i++) {
      varfn = varf0 + varf1;
      varf0 = varf1;
      varf1 = varfn;
    }
    return varfn;
  }
  
  printf("%d", my_func(9));
}
```

## Arrays as Function Parameters

Starting from **v0.24**, HyperDbg supports arrays (including multi-dimensional arrays) as parameters to functions.

In the following example, `myCalculateSum` takes a one-dimensional array along with its element count and returns the sum of its elements. `myPrintMatrix` takes a two-dimensional array along with the number of rows and prints its contents.

```c
? {
    int myCalculateSum(int values[], int count) {
        int sum = 0;

        for (int i = 0; i < count; i++) {
            sum += values[i];
        }

        return sum;
    }

    void myPrintMatrix(int matrix[][3], int rows) {
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < 3; j++) {
                printf("%d ", matrix[i][j]);
            }

            printf("\n");
        }
    }

    //
    // One-dimensional array example
    //
    int values[5] = {0n10, 0n20, 0n30, 0n40, 0n50};
    int sum = myCalculateSum(values, 5);

    printf("Sum: %d\n", sum);

    //
    // Two-dimensional array example
    //
    int matrix[2][3];

    matrix[0][0] = 1;
    matrix[0][1] = 2;
    matrix[0][2] = 3;

    matrix[1][0] = 4;
    matrix[1][1] = 5;
    matrix[1][2] = 6;

    printf("Matrix:\n");
    myPrintMatrix(matrix, 2);
}
```

## Constants

At the moment, **dslang** does not support constants but the support for these items will be added to the script-engine in future versions!
