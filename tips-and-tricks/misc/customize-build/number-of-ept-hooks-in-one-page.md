---
description: Increasing the number of EPT hooks in one page
---

# Number of EPT Hooks in One Page

If HyperDng displays an error message stating that the maximum number of breakpoints in a single page is hit (e.g., "`err, the maximum breakpoint for a single page is hit, you cannot apply more breakpoints in this page`"), you can increase the number to avoid this error.

To fix this problem, you must build a customized version of HyperDbg that uses the most suitable number for your requirements.

Navigate to the source code and locate the following macro. In the header files, search for `MaximumHiddenBreakpointsOnPage` and you will see the following code:

```clike
/**
 * @brief Maximum number of hidden breakpoints in a page
 *
 */
#define MaximumHiddenBreakpointsOnPage 40
```

The above macro defines the default number of breakpoints that are possible to put on a single page (4096 bytes). You must increase it according to your estimation. After that, [rebuild](https://docs.hyperdbg.org/getting-started/build-and-install) HyperDbg, and use your custom-made version of the debugger.
