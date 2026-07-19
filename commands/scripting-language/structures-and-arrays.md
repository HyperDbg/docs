---
description: Description of structures and arrays
---

# Structures & Arrays

## Structures

HyperDbg supports defining and using structures within scripts, similar to C structs.

### Defining and Initializing a Struct

Structures are defined using the `struct` keyword, and instances can be initialized with brace-enclosed values.

```c
? {
  struct Point {
      int x;
      int y;
  };

  struct Point local = {0n31, 0n32};
  printf("%d %d\n", local.x, local.y);
}
```

The above example prints:

```
31 32
```

### Accessing and Modifying Struct Members

Struct members are accessed using the dot (`.`) operator. They can be read and modified like regular variables.

```c
? {
  struct Point {
      int x;
      int y;
  };

  struct Point local = {0n31, 0n32};
  printf("%d %d\n", local.x, local.y);

  local.x += 5;
  local.y = 1 + local.y;
  printf("%d %d\n", local.x, local.y);
}
```

The above example prints:

```
31 32
36 33
```

### Pointers to Structs (Arrow Operator)

You can take the address of a struct instance using `&` and access its members via a pointer using the arrow (`->`) operator.

```c
? {
  struct ArrowNestedValue {
      int value;
  };

  struct ArrowNestedContainer {
      struct ArrowNestedValue nested;
  };

  struct ArrowNestedContainer arrow_nested;
  struct ArrowNestedContainer *arrow_nested_pointer;
  arrow_nested_pointer = &arrow_nested;
  arrow_nested_pointer->nested.value = 0n50;

  if (arrow_nested.nested.value == 0n50 &&
      arrow_nested_pointer->nested.value == 0n50) {
      printf("%d\n", arrow_nested_pointer->nested.value);
  }
}
```

The above example prints:

```
50
```

### Nested Structs

As shown in the pointer example above, structs can be nested (a struct may contain another struct as a member field). Both dot (`.`) and arrow (`->`) operators can be chained to reach deeply nested members (e.g., `arrow_nested_pointer->nested.value`).

## Arrays

HyperDbg also supports arrays within scripts.

### Initializing Array

The following statements are used for initializing arrays.

```c
int t1 [3] = {0};
```

### Array Assignment

The following example shows how to assign constant values to an array.

```c
int t1 [6] = {1,2,3,4,5,6};
```

You can also assign variables to an array.

```c
t44 = 55;
int t1[6] = {1,2,3,t44,5,6};
```

### Reading Array Elements

There are two ways you can read each array item: first, by using its index, or by dereferencing it directly from memory.

By using its index:

```c
int t1[6] = {1,2,3,4,5,6};

for (i = 0; i < 6 ; i++){
	printf("%d\n", t1[i]);
}
```

By directly reading it from memory:

```c
int t1[6] = {1,2,3,4,5,6};

for( i = 0; i < 6 ; i++){
	printf("%d\n", *(i+t1));
}
```

### Multidimensional Array

Multidimensional arrays are defined as follows.

```c
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

```c
int t1 [3][3][3] = {{1,2,3,4},{4,5,6,4},{7,8,9,4}};

printf("%d\n", t1[0][0][3]); // prints 4
```
