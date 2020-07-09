---
description: This document helps you to build and install HyperDbg
---

# Build & Install

### Download & Install

In order to build HyperDbg, you need to install the following software.

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

### Run & Test

On the x64 Windows, you have to enable **test-signing** to run driver. By default, HyperDbg enables test-signing when you open **hyperdbg-gui.exe** or **hyperdbg-cli.exe**, if test-signing is disabled in your system you have to restart your system to run HyperDbg.



