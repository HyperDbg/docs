---
description: Description of data types and operators
---

# Data types & Operators

## Data Types

HyperDbg's script engine supports several data types. By default, all variables declared without an explicit type are treated as **unsigned 64-bit integers** (`unsigned long long`). Starting from **v0.23**, the script engine also supports **floating-point** (`float`) and **double-precision** (`double`) types.

### int

The `int` type represents a signed 32-bit integer. It can hold values in the range of approximately −2 billion to +2 billion.

**Example 1: Basic integer declaration and printing**

```c
? {
    int x = 0n42;
    int y = 0n100;
    printf("x = %d, y = %d\n", x, y);
}
```

Output:

```
x = 42, y = 100
```

**Example 2: Integer in a loop**

```c
? {
    int sum = 0;
    for (int i = 1; i <= 5; i++) {
        sum += i;
    }
    printf("sum of 1..5 = %d\n", sum);
}
```

Output:

```
sum of 1..5 = 15
```

### float

The `float` type represents a single-precision (32-bit) IEEE 754 floating-point number. Floating-point literals can be written with a leading digit (`11.5`), a leading dot (`.5`), or a trailing dot (`11.`). Starting from **v0.23**.

**Example 1: Declaration and printing**

```c
? {
    float var1 = 11.5;
    float var2 = 0.5;
    float var3 = .5;
    float var4 = 11.;
    float negative1 = -11.5;
    float negative2 = -.5;

    printf("floating point: %f %f %f %f\n", var1, var2, var3, var4);
    printf("negative floating point: %f %f\n", negative1, negative2);
}
```

Output:

```
floating point: 11.500000 0.500000 0.500000 11.000000
negative floating point: -11.500000 -0.500000
```

**Example 2: Arithmetic operations**

```c
? {
    float floatAdd = 11.5 + 0.5;
    float floatSub = 11.5 - 0.5;
    float floatMul = 1.5 * 2.0;
    float floatDiv = 7.5 / 2.5;

    printf("add=%f sub=%f mul=%f div=%f\n", floatAdd, floatSub, floatMul, floatDiv);
}
```

Output:

```
add=12.000000 sub=11.000000 mul=3.000000 div=3.000000
```

### double

The `double` type represents a double-precision (64-bit) IEEE 754 floating-point number, providing more significant digits than `float`. Starting from **v0.23**.

**Example 1: Declaration and printing**

```c
? {
    double var5 = 0.789;
    double negative3 = -0.789;
    double negativeZero = -0.0;
    double positive = +.5;

    printf("double: %f\n", var5);
    printf("negative double: %f %f\n", negative3, negativeZero);
    printf("positive: %f\n", positive);
    printf("runtime negative: %f\n", -var5);
}
```

Output:

```
double: 0.789000
negative double: -0.789000 -0.000000
positive: 0.500000
runtime negative: -0.789000
```

**Example 2: Arithmetic operations**

```c
? {
    double doubleAdd = 0.75 + 0.25;
    double doubleSub = 5.5 - 2.0;
    double doubleMul = 1.25 * 4.0;
    double doubleDiv = 9.0 / 4.0;

    printf("add=%f sub=%f mul=%f div=%f\n", doubleAdd, doubleSub, doubleMul, doubleDiv);
}
```

Output:

```
add=1.000000 sub=3.500000 mul=5.000000 div=2.250000
```

**Example 3: Mixed float and double, operator precedence**

```c
? {
    float mixedSingle = 1.5;
    double mixedResult = mixedSingle + 0.25;
    double precedenceResult = 1.0 + 2.0 * 3.0;
    double negativeResult = -2.0 * 3.0;

    printf("mixed=%f precedence=%f negative=%f\n", mixedResult, precedenceResult, negativeResult);
}
```

Output:

```
mixed=1.750000 precedence=7.000000 negative=-6.000000
```

**Example 4: Comparisons with floating-point values**

```c
? {
    float floatAdd = 11.5 + 0.5;
    float floatSub = 11.5 - 0.5;
    double negativeZero = -0.0;

    if (floatAdd > floatSub && floatSub >= 11.0 &&
        floatSub < floatAdd && floatSub <= 11.0 &&
        floatAdd != floatSub && negativeZero == 0.0)
    {
        printf("floating arithmetic was successful\n");
    }
    else
    {
        printf("floating arithmetic was failed\n");
    }
}
```

Output:

```
floating arithmetic was successful
```

## Operators

### sizeof

The `sizeof` operator returns the size (in bytes) of a type, a variable, or a struct. It works with all built-in types (`char`, `short`, `int`, `long`, `long long`), user-defined structs, and implicitly-typed variables. Support for `sizeof` was added starting from **v0.23**.

**Syntax**

```c
sizeof(type_or_variable)
```

**Example 1: sizeof with built-in types and a struct**

```c
? {
    struct VariableTypePair {
        int left;
        unsigned short right;
    };

    printf("char=%lld\n", sizeof(char));
    printf("short=%lld\n", sizeof(short));
    printf("int=%lld\n", sizeof(int));
    printf("long=%lld\n", sizeof(long));
    printf("long long=%lld\n", sizeof(long long));
    printf("struct=%lld\n", sizeof(struct VariableTypePair));
}
```

Output:

```
char=1
short=2
int=4
long=8
long long=8
struct=8
```

**Example 2: sizeof with an implicit variable**

By default, variables declared without an explicit type are treated as `unsigned long long` (8 bytes).

```c
? {
    implicit_variable = 0xffffffffffffffff;  // the default variable type is unsigned long long

    printf("value=%llu\n", implicit_variable);
    printf("size=%lld\n", sizeof(implicit_variable));

    if (implicit_variable > 0) {
        printf("implicit variable is unsigned\n");
    }
}
```

Output:

```
value=18446744073709551615
size=8
implicit variable is unsigned
```

{% hint style="info" %}
`sizeof` evaluates at compile time for types and at parse time for variables. It does not execute any runtime expressions - it only measures the storage size of the given type or variable.
{% endhint %}
