---
description: This document helps you to build and install HyperDbg
---

# Build & Install

### Download & Install

To build HyperDbg, you need to install the following software.

**Visual Studio Community, Enterprise or Professional 2015 or later.**

* You can download it at: \[[https://www.visualstudio.com/downloads](https://www.visualstudio.com/downloads/)\]

**Windows Software Development Kit \(Windows SDK\) for Windows 10**

*  You can download it at: \[[https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk)\]

**Windows Driver Kit \(WDK\)** 

* You can download it at: \[[https://developer.microsoft.com/en-us/windows/hardware/windows-driver-kit](https://developer.microsoft.com/en-us/windows/hardware/windows-driver-kit)\]

### Build & Compile

First, clone the project using git or download it [here](https://github.com/HyperDbg/HyperDbg/archive/master.zip).

```
git clone --recursive https://github.com/HyperDbg/HyperDbg.git
```

Open the visual studio and **build** the solution.

After building, you need to disable driver signature enforcement. It is because the current versions of **HyperDbg** are not digitally signed.

For this purpose, you can **Disable Driver Signature Enforcement** or **Put Windows in Test Mode**.

### Disable Driver Signature Enforcement

This is the simplest way to disable driver signature enforcement on Windows 10 but bear in mind that this method will temporarily disable driver signature enforcement.

After you restart, your computer driver signature enforcement will automatically turn itself on.

To disable driver signature enforcement, do the following:

1. Press and hold the **Shift** key on your keyboard and click the **Restart** button. 
2. Choose to **Troubleshoot &gt; Advanced options &gt; Startup Settings** and click the **Restart** button.
3. When your computer restarts, you’ll see a list of options. Press **F7** on your keyboard to select **Disable driver signature enforcement**.
4. Your computer will now restart, and you’ll be able to install unsigned drivers.

Bear in mind that this method only temporarily disables driver signature enforcement, and after a restart, you have to re-disable it again.

### Put Windows in Test Mode

In test mode, you can install any drivers you want without experiencing any problems. Don’t forget to go to normal Windows 10 mode after you solve your problem:

1. Open an elevated command prompt window on your PC: right-click on the Windows Start icon and select **Command prompt \(Admin\)**.
2. In cmd type:`bcdedit /set TESTSIGNING ON`
3. Close the cmd window and restart your computer.
4. Install your drivers.

For more options, please visit [here](https://windowsreport.com/driver-signature-enforcement-windows-10/).

### Run & Test

Open **hyperdbg-cli.exe**, and visit [Quick Start](https://docs.hyperdbg.com/getting-started/quick-start) to start using **HyperDbg**.



