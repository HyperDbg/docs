---
description: Description of structures and arrays
---

# Structures & Arrays

## Arrays

HyperDbg also supports arrays within scripts.

### Initializing Array

The following statements are used for initializing arrays.

```clike
int t1 [3] = {0};
```

### Array Assignment

The following example shows how to assign constant values to an array.

```clike
int t1 [6] = {1,2,3,4,5,6};
```

You can also assign variables to an array.

```clike
t44 = 55;
int t1[6] = {1,2,3,t44,5,6};
```

### Reading Array Elements

There are two ways you can read each array item: first, by using its index, or by dereferencing it directly from memory.

By using its index:

```clike
int t1[6] = {1,2,3,4,5,6};

for (i = 0; i < 6 ; i++){
	printf("%d\n", t1[i]);
}
```

By directly reading it from memory:

```clike
int t1[6] = {1,2,3,4,5,6};

for( i = 0; i < 6 ; i++){
	printf("%d\n", *(i+t1));
}
```

### Multidimensional Array

Multidimensional arrays are defined as follows.

```clike
int t1 [3][3] = {{1,2,3},{4,5,6},{7,8,9}};

printf("%d\n",t1[0][0]); // prints 1
printf("%d\n",t1[0][1]); // prints 2
printf("%d\n",t1[0][2]); // prints 3

printf("%d\n",t1[1][0]); // prints 4
printf("%d\n",t1[1][1]); // prints 5
printf("%d\n",t1[1][2]); // prints 6

printf("%d\n",t1[2][0]); // prints 7
printf("%d\n",t1[2][1]); // prints 8
printf("%d\n",t1[2][2]); // prints 9
```

or another example with more dimensions:

```clike
int t1 [3][3][3] = {{1,2,3,4},{4,5,6,4},{7,8,9,4}};

printf("%d\n",t1[0][0][3]); // prints 4
```

## Modify Memory

Modifying memory is possible using '[eb, ed, eq](https://docs.hyperdbg.org/commands/scripting-language/functions/eb-ed-eq)' functions.

`eb` modifies a single `byte`.

`ed` modifies a `dword`.

`eq` modifies a `qword` value.

The following code edits memory (**quad-word**) at `fffff8031d44fde0` and change it to `0x12345678deadbeef`.

```c
IsEditApplied = eq(fffff8031d44fde0, 0x12345678deadbeef);
```

The following code changes a **byte** to 0x90 at the location that the **@rcx** register is pointing to, then adds 0x8 to it.

```c
IsEditApplied = eb(poi(@rcx)+8, 0x90);
```
