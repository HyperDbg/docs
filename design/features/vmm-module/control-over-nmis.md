---
description: Different controlling fields about NMIs in Intel VT-x
---

# Control over NMIs

While an NMI interrupt handler is executing, the processor blocks the delivery of subsequent NMIs until the next IRET instruction execution. This blocking of NMIs prevents the nested execution of the NMI handler.

Execution of the IRET instruction unblocks NMIs even if the instruction causes a fault. For example, if the IRET instruction executes with EFLAGS.VM = 1 and IOPL of less than 3, a general-protection exception is generated. In such a case, NMIs are unmasked before the exception handler is invoked.

Interruptibility state \(32 bits\). The IA-32 architecture includes features that permit certain events to be blocked for a period of time.

## **NMI Controller Bits In VMX**

The following sections demonstrate the bits that control the NMI behavior in Intel Vmx.

### **NMI Exiting \(Pin-Based VM-Execution Controls\)**

If the NMI Exiting control is 0 and an NMI arrives while in VMX-non-root mode, the NMI is delivered to the guest via the guest's IDT. If the NMI Exiting control is 1, an NMI causes a VM exit.

In other words, if this control is 1, non-maskable interrupts \(NMIs\) cause VM exits. Otherwise, they are delivered normally using descriptor 2 of the IDT.

### **Blocking by NMI** \(**Interruptibility state**\)

Delivery of a non-maskable interrupt \(NMI\) blocks subsequent NMIs until the next execution of IRET.

Setting this bit indicates that blocking of NMIs is in effect. Clearing this bit does not imply that NMIs are not blocked for other reasons.

If the “virtual NMIs” VM-execution control is 1, this bit does not control the blocking of NMIs. Instead, it refers to “virtual-NMI blocking” \(the fact that guest software is not ready for an NMI\).

### **NMI-window exiting \(Primary Proc-Based VM Controls\)**

It's a "window exiting" field, "window exiting" means that there is an open Windows or there is an opportunity to deliver the NMI when you continue the execution \(VMRESUME\).

Intel described it this way,

If this control is 1, a VM exit occurs at the beginning of any instruction if there is no virtual NMI blocking.

But in short, when the guest is ready to deliver an NMI, and there is nothing to block this NMI, then a vm-exit occurs if you set this bit.

### **Virtual NMIs \(Pin-Based VM-Execution Controls\)**

If this control is 1, NMIs are never blocked, and the “blocking by NMI” bit \(bit 3\) in the interruptibility-state field indicates “virtual-NMI blocking”.

### **IRET & NMI Unblocking**

The behavior of IRET with regard to NMI blocking \(see Table 24-3\) is determined by the settings of the “NMI exiting” and “virtual NMIs” VM-execution controls:

— If the “NMI exiting” VM-execution control is 0, IRET operates normally and unblocks NMIs. \(If the “NMI exiting” VM-execution control is 0, the “virtual NMIs” control must be 0; see Section 26.2.1.1.\)

— If the “NMI exiting” VM-execution control is 1, IRET does not affect the blocking of NMIs. If, in addition, the “virtual NMIs” VM-execution control is 1, the logical processor tracks virtual-NMI blocking. In this case,

IRET removes any virtual-NMI blocking. The unblocking of NMIs or virtual NMIs specified above occurs even if IRET causes a fault.

