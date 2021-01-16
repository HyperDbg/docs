---
description: Design of !syscall and !sysret command
---

# Design of !syscall & !sysret

The idea of these commands are derived from this blog post :

{% embed url="https://revers.engineering/syscall-hooking-via-extended-feature-enable-register-efer/" %}

If you want a comprehensive explanation, please read the above link but a short explanation described here.

This command unsets the **Syscall Enable** Bit \(SE Bit\) in **EFER** MSR. 

If we unset this bit, then execution of **SYSCALL** or **SYSRET** causes a **\#UD** or undefined opcode exception.

We can intercept **\#UD**s using **Exception Bitmap** of VMCS. In the vm-exit, we can check whether the generated **\#UD** was because of a **SYSCALL** or **SYSRET** instructions and if it was true then we emulate the **user-to-kernel** or **kernel-to-user** act of these instructions. If it was not because of **SYSCALL** or **SYSRET**, we inject **\#UD** back to the guest.

The check for these instructions is performed by checking the memory content of the **GUEST\_RIP** field of VMCS.

PatchGuard detects this command, so it's essential to attach a Windbg kernel debugger so the PatchGuard won't start, and you can use this command without any problem.



