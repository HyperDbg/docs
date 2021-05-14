---
description: Description of conditional statements and loops
---

# Conditionals & Loops

## Conditional Statements

In HyperDbg and all other programming and scripting languages, conditional statements are used to perform different actions based on different conditions.

### if

The `if` statement executes some code if one condition is **true**.

**Syntax**

```c
if (condition) {
  code to be executed if condition is true;
}
```

**Example 1**

```c
if (@rax == 55) {
    printf("rax is equal to %llx\n", @rax);
}
```

**Example 2**

```c
if (poi(@rcx + 0x10) == ffff7080deadbeef && @rdx != 55 || $pid == 4) {
    printf("condition is met\n");
}
```

### else

Executes some code if a condition is **true** and another code if that condition is **false**.

**Syntax**

```c
if (condition) {
  code to be executed if condition is true;
}
else {
  if the above condition is false, then else is called;
}
```

**Example**

```c
if (@rax == 55) {
    printf("rax is equal to %llx\n", @rax);
}
else {
    printf("rax is not equal to 0x55, it is equal to %llx\n", @rax);
}
```

### elsif

Multiple `if...else` statements can be nested to create an `elsif` clause. Note that there is one `elsif` \(in one word\) keyword in HyperDbg script engine.

**Syntax**

```c
if (condition) {
  code to be executed if condition is true;
}
elsif (condition) {
  code to be executed if elsif condition is true;
}
else {
  if none of the above conditions are true, then else is called;
}
```

**Example**

```c
if (@rax == 55) {
    printf("rax is equal to 0x55\n");
}
elsif (@rax == 66) {
    printf("rax is equal to 0x66\n");
}
elsif (@rax == 77) {
    printf("rax is equal to 0x77\n");
}
else {
    printf("rax is not equal to 0x55, 0x66, 0x77. It is equal to %llx\n", @rax);
}
```

## Loops

The following statements are used to create loops in HyperDbg's script engine.

### for

 The **for statement** creates a loop that consists of three expressions, enclosed in parentheses and separated by semicolons, followed by a block statement to be executed in the loop.

**Syntax**

```c
for (initial value; condition; incrementation or decrementation) {
  code to be executed at least one time and continues,
   if while condition is true;
}
```

**Example 1**

```c
for (i = 10; i != 0; i--) { 
    printf("%d\n",i); 
}
```

**Example 2**

```c
for ( i = 0; i < 10 ; i--) {
    for (j = 0; j < 10; j++) {
     printf("%d, %d\n", i, j); 
   }
 }
```

### while

With the `while` loop we can execute a set of statements as long as a condition is **true**.

**Syntax**

```c
while (condition) {
  code to be executed if while condition is true;
}
```

**Example**

```c
x = 55;
while (x) {
    printf("x = %x\n", x);
    x = x - 1;
}
```

### do, while

The `do..while` loop is similar to the `while` loop with one important difference. The body of `do...while` loop is executed at least once. Only then, the test expression is evaluated.

**Syntax**

```c
do {
  code to be executed at least one time and continues,
   if while condition is true;
}
while (condition); 
```

**Example**

```c
x = 55;
do {
    printf("x = %x\n", x);
    x = x - 1;
} while (x);
```

