---
description: This guide introduces a consistent style for writing codes for HypedDbg.
---

# Coding Style

### Naming Style

For functions and variables, you should start with capital case characters, and for each word, you should start with capital characters. You should not use underline `_` on these names.

For structures and enum names, you should write them all in capital characters and separate each word by one underline `_` .

### Clang Format

We use [this ](https://github.com/HyperDbg/HyperDbg/blob/master/hyperdbg/hprdbghv/.clang-format)clang-format for writing codes in our C driver.

Please make sure to format the code using clang format's visual studio extension or clang command-line tools.

### Comments

Putting comments on the code is very important. This way, other people can understand your code better and will be used for further improvement.

Please put the comment in this style.

```c
//
// This is my comment line 1
// This is my comment line 2
//
```

### Global Variables

All global variables should start with `g_` for example `g_GuestState`.

### File Names

For drivers, file names start with a capital case character.

For user-mode codes, file names start with lower case characters. 

### Printing Messages

In HyperDbg, in most cases, if the printed message came from kernel-mode or vmx-root mode, then the first letter is capitalized, while if the message is generated from the user-mode debugger, it starts with lower-case characters.

### Error Styles

Exactly, like the above conventions for "Printing Messages", errors follow the same rule.

In HyperDbg, errors might come from either user-mode codes, kernel-mode, or vmx-root mode. Because we want to know the root cause of the error, we follow the rule of **"`Err,` "** and **"`err,` "**.

If the error starts with `err,` then, it means that the user-mode code generated this error. If the error started with `Err,` it means that it was either kernel-mode or vmx-root mode that encountered this error.



