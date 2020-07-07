---
description: What is vmx-root mode and what is vmx non-root mode?
---

# Vmx root-mode vs Vmx non-root mode

By vmx-root code execution in HyperDbg, we mean that the code is executed after a vm-exit and before VMRESUME instruction.

By vmx non-root, it means that the code will be executed in regular kernel-mode codes.

You should be cautious if you want to cause vm-exit in vmx non-root. For example, you should not execute CPUID in vmx-root as it makes your logging mechanism slower, in other words, there is no limitation but you have to know what you want to do.

Please make sure to avoid ["unsafe" behavior](https://docs.hyperdbg.com/tips-and-tricks/considerations/the-unsafe-behavior) in vmx-root mode.

