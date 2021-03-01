# Printf

### Function

> Printf

### Syntax

> Printf\(Format, Expressions \[...\] \);

### Parameters

\[Expression\]

         A [MASM-like expression](https://docs.hyperdbg.com/commands/scripting-language/assumptions-and-evaluations) to evaluate.

### Description

Evaluates and prints a MASM expression.

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

`print(@rcx);`

Print data as an ASCII string pointed by **rcx** register.

`print(dq(@rcx));`

Print data as an 8-byte hex, pointed by **rcx** register.

`print($proc+@rdx);`

Print value pointed by `$proc+@rdx`  which **$proc** is equivalent to current `_EPROCESS`  added to the **rdx** register.

{% hint style="success" %}
You can see more examples [here](https://docs.hyperdbg.com/commands/scripting-language/examples/view-system-state).
{% endhint %}

### **Remarks**

You should consider avoiding ["unsafe" behavior](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior) in your expressions.

