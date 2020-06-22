---
description: This guide introduces a consistent style for writing codes for HypedDbg.
---

# Coding Style

### Naming Style

For functions and variables, you should start with capital case character and for each word you should start with capital characters. You should not use underline `_` on these names.

For structures and enum names you should write them all in capital characters and separate each word by one underline `_` .

### Clang Format

We use [this ](https://github.com/HyperDbg/HyperDbg/blob/master/hyperdbg/hprdbghv/.clang-format)clang format for writing codes in our C driver.

Please make sure to format the code using clang format's visual studio extension or clang command-line tools.

### Comments

Putting comments on the code is very important, this way other people can understand your code better and will be used for further improvement.

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

