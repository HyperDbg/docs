# Log the State's LL1 Grammer

### LL1 Grammar

```text
S -> C;S’
S’ -> C;S’ 
S’ -> λ
C -> name = E0;

E0 -> E1 E0’
E0’ -> | E1 E0’
E0’ -> λ

E1 ->  E2 E1’
E1’ -> ^ E2 E1’ 
E1’ -> λ 

E2 ->  E3 E2’
E2’ -> & E3 E2’ 
E2’ -> λ 

E3 -> E4 E3’
E3’ -> >> E4 E3’
E3’ -> λ

E4 -> E5 E4’
E4’ -> << E5 E4’ 
E4’ -> λ

E5 -> E6 E5’
E5’ -> + E6 E5’
E5’ -> λ

E6 -> E7 E6’
E6’ -> - E7 E6’ 
E6’ -> λ

E7 -> E8 E7’
E7’ -> * E8 E7’ 
E7’ -> λ

E8 -> E9 E8’
E8’ -> / E9 E8’
E8’ -> λ

E9 -> poi(Expr) 
E9 -> db(Expr) 
E9 -> dd(Expr) 
E9 -> dw(Expr) 
E9 -> dq(Expr) 
E9 -> str(Expr) 
E9 -> wstr(Expr) 
E9 -> sizeof(Expr) 
E9 -> not(Expr) 
E9 -> neg(Expr) 

E9 -> @reg 
E9 -> num  
E9 -> func 
E9 -> $meta
```

### Examples

```text
test=str(@rcx)
test=dq(@rcx)
test=$proc+@rdx
test=poi(@rax+a0)
test1=wstr(poi($proc+10));test2=str(poi($proc+10));
test=dw(NtCreateFile+10)
test=dw(NtCreateFile+@rcx+($proc|3+poi(poi(@rax))))
```

### Description

`test=str(@rcx)`

One field, called '**test**', which will be read as an ASCII string pointed by **rcx** register.

`test=dq(@rcx)`

One field, called '**test**', which will be read as an 8-byte hex pointed by **rcx** register.

`test=$proc+@rdx`

One field, called '**test**', which is equivalent to **$proc** \(current `_EPROCESS`\) added to the **rdx** register.

`test=poi(@rax+a0)` 

One field, called '**test**', which first, **rax** register is added with **0xa0** constant then a dereference occurs and the target is shown as a QWORD hex.

`test1=wstr(poi($proc+10));test2=str(poi($proc+10));`

Three fields, first '**test1**' which is **$proc** \(current `_EPROCESS`\) added with **0x10** then a dereference occurs and the target pointer in the dereferenced location is shown as a wide-char string.

Second, '**test2**' which is **$proc** \(current `_EPROCESS`\) added with **0x10** then a dereference occurs and the target pointer in the dereferenced location is shown as an ASCII string.

`test=dw(NtCreateFile+10)`

One field, called '**test**', **NtCreateFile** location is added with **0x10** then a DWORD value from the target address \(**NtCreateFile+10**\) is shown.

{% hint style="success" %}
By default, **HyperDbg** interprets the numbers as **hex** \(base **16**\), if you want to specify other forms of a number you should use MASM prefixes. In all MASM expressions, numeric values are interpreted as numbers in the current radix \(16, 10, or 8\). You can override the default radix by specifying the **0x** prefix \(**hexadecimal**\), the **0n** prefix \(**decimal**\), the **0t** prefix \(**octal**\), or the **0y** prefix \(**binary**\).
{% endhint %}

