---
description: Description of 'printf' function in HyperDbg Scripts
---

# printf

### Function

> printf

### Syntax

> printf\( Format, Expressions \[...\] \);

### Parameters

**\[Format-string\]**

         A printf-like format string.

**\[Expression\] \[...\]**

         [MASM-like expression\(s\)](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations) to evaluate, you can choose multiple expressions based on  the format string

### Description

Evaluates and prints a printf-like format string with MASM expression\(s\).

{% hint style="danger" %}
All the format specifiers should be in a lower-case format.
{% endhint %}

### Supported Format Specifiers

| Specifier | Description |
| :--- | :--- |
| **%d** | Decimal signed integer |
| **%i** | Decimal signed integer |
| **%u** | Decimal unsigned integer |
| **%o** | Octal integer |
| **%x** | Hex integer |
| **%c** | Character \(char\) |
| **%p** | Pointer \(void \*\) |
| **%s** | String |
| **%ws** | Wide-String |
| **%ls** | Wide-String |

### Integer Format Specifiers \(Long\)

| Specifier | Description |
| :--- | :--- |
| **%ld** | Long decimal signed integer |
| **%li** | Long decimal signed integer |
| **%lu** | Long decimal unsigned integer |
| **%lo** | Long octal integer |
| **%lx** | Long hex integer |

### Integer Format Specifiers \(Long Long\)

| Specifier | Description |
| :--- | :--- |
| **%lld** | Long long \(64-bit\) decimal signed integer |
| **%lli** | Long long \(64-bit\) decimal signed integer |
| **%llu** | Long long \(64-bit\) decimal unsigned integer |
| **%llo** | Long long \(64-bit\) octal integer |
| **%llx** | Long long \(64-bit\) hex integer |

### Integer Format Specifiers \(Short\)

| Specifier | Description |
| :--- | :--- |
| **%hd** | Short decimal signed integer |
| **%hi** | Short decimal signed integer |
| **%hu** | Short decimal unsigned integer |
| **%ho** | Short octal integer |
| **%hx** | Short hex integer |

### Examples

`printf("Result is %s", @rcx);`

Print data as an ASCII string pointed by **rcx** register.

`print("The value is %llx and the string is : %ws "dq(@rcx), poi(rax));`

Print and evaluate the above expressions. Note that **%ws** is a wide-string pointed by **rax** register. Also, the value of **rcx** register is also dereferenced and showed in quad-hex format.

{% hint style="success" %}
You can see more examples [here](https://docs.hyperdbg.com/commands/scripting-language/examples/view-system-state).
{% endhint %}

### **Remarks**

You should consider avoiding ["unsafe" behavior](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior) in your expressions and strings.

