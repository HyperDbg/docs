---
description: Description of 'x' command in HyperDbg.
---

# x \(examine symbols and find functions and variables address\)

### Command

> x

### Syntax

> x \[name \(string - wildcard\)\]

### Description

Executes a single instruction \(step-over\) and optionally displays the resulting values of all registers and flags.

### Parameters

**\[count\] \(optional\)**

          Specifies a pattern that the symbol must contain. _Symbol_ can contain a variety of wildcard characters and specifiers. For more information about the syntax, see [String Wildcard Syntax](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/string-wildcard-syntax).  


### Examples

### IOCTL

None

### **Remarks**

<table>
  <thead>
    <tr>
      <th style="text-align:left">Value</th>
      <th style="text-align:left">Meaning</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>foo</b>
      </td>
      <td style="text-align:left">
        <p>If BaseOfDll is not zero, then <b>SymEnumSymbols</b> will look for a global
          symbol named &quot;foo&quot;.</p>
        <p>If BaseOfDll is zero, then <b>SymEnumSymbols</b> will look for a local symbol
          named &quot;foo&quot; within the scope established by the most recent call
          to the <a href="https://docs.microsoft.com/en-us/windows/desktop/api/dbghelp/nf-dbghelp-symsetcontext">SymSetContext</a> function.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>foo?</b>
      </td>
      <td style="text-align:left">
        <p>If BaseOfDll is not zero, then <b>SymEnumSymbols</b> will look for a global
          symbol that starts with &quot;foo&quot; and contains one extra character
          afterwards, such as &quot;fool&quot; and &quot;foot&quot;.</p>
        <p>If BaseOfDll is zero, then <b>SymEnumSymbols</b> will look for a symbol
          that starts with &quot;foo&quot; and contains one extra character afterwards,
          such as &quot;fool&quot; and &quot;foot&quot;. The search would be within
          the scope established by the most recent call to the <a href="https://docs.microsoft.com/en-us/windows/desktop/api/dbghelp/nf-dbghelp-symsetcontext">SymSetContext</a> function.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>foo*!bar</b>
      </td>
      <td style="text-align:left"><b>SymEnumSymbols</b> will look in every loaded module that starts with
        the text &quot;foo&quot; for a symbol called &quot;bar&quot;. It could
        find matches such as these, &quot;foot!bar&quot;, &quot;footlocker!bar&quot;,
        and &quot;fool!bar&quot;.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>*!*</b>
      </td>
      <td style="text-align:left"><b>SymEnumSymbols</b> will enumerate every symbol in every loaded module.</td>
    </tr>
  </tbody>
</table>

### Requirements

None

### Related

None

