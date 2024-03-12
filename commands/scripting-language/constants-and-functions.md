---
description: Description of constants and functions
---

# Constants & Functions

## Functions

### Void Function

```clike
? {
    
  void myfun(int var, int var2) {
    printf("var = %d, var2 = %d\n", var, var2);
    return;
    printf("222\n");
  }
  
  for (int i = 0; i < 3; i++) {
    myfun(1, 2);
  }

  int myvar = 79;
  printf("%x\n", myvar);
}
```

### Int Function

```clike
? {
    
  int myfun1(int var1) {
    result = var1 + 1;
    printf("myfun1 %d\n", result);
    return result;
  }
  
  int myfun2(int var1) {
    result = var1 * 2;
    printf("myfun2 %d\n", result);
    return result;
  }
  
  int myfun3(int var1) {
    result = var1 * 2;
    printf("myfun3 %d\n", result);
    return result;
  }
  
  int myfun4(int var1) {
    result = myfun1(var1) + myfun2(var1) + myfun3(var1);
    printf("myfun4 %d\n", result);
    return result;
  }

  printf("%d\n", myfun4(2));
}
```

#### Example 1: Recursive Fibonacci

```clike
? {

  int myfun(int var1) {
    if (var1 == 0) {
      return 0;
    }
    if (var1 == 1) {
      return 1;
    }
    return myfun(var1 - 1) + myfun(var1 - 2);
  }
  
  var = myfun(9);
  printf("%d", var);
}
```

#### Example 2: Iterative Fibonacci

```clike
? {

  int myfun(int varn) {
      
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
  
  printf("%d", myfun(9));
}
```

## Constants

At the moment, **dslang** does not support constants but the support for these items will be added to the script-engine in future versions!
