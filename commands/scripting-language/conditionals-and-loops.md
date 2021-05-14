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

**Examples**

```c

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

**Examples**

```c

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

**Examples**

```c

```

## Loops

The following statements are used to create loops in HyperDbg's script engine.

### while

With the `while` loop we can execute a set of statements as long as a condition is **true**.

**Syntax**

```c
while (condition) {
  code to be executed if while condition is true;
}
```

**Examples**

```c

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

**Examples**

```c

```

### for

 The **for statement** creates a loop that consists of three expressions, enclosed in parentheses and separated by semicolons, followed by a block statement to be executed in the loop.

**Syntax**

```c
for (initial value; condition; incrementation or decrementation) {
  code to be executed at least one time and continues,
   if while condition is true;
}

```

**Examples**

```c

```

