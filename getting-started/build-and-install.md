---
description: This document helps you to build and install HyperDbg
---

# Build & Install

## Download & Install

To build HyperDbg, you need to install the following software.

**Visual Studio Community, Enterprise or Professional 2015 or later.**

* You can download it at: \[[https://www.visualstudio.com/downloads](https://www.visualstudio.com/downloads/)]

**Windows Software Development Kit (Windows SDK)**

* You can download it at: \[[https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/](https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/)]

**Windows Driver Kit (WDK)**

* You can download it at: \[[https://developer.microsoft.com/en-us/windows/hardware/windows-driver-kit](https://developer.microsoft.com/en-us/windows/hardware/windows-driver-kit)]

## Build & Compile

First, clone the project using **git** or download precompiled binaries from [here](https://github.com/HyperDbg/HyperDbg/releases).

```
git clone --recursive https://github.com/HyperDbg/HyperDbg.git
```

Open the visual studio and **build** the solution.

After building, you need to disable driver signature enforcement. It is because the current versions of **HyperDbg** are not digitally signed.

For this purpose, you can **Disable Driver Signature Enforcement** or **Put Windows in Test Mode**.

## Running HyperDbg

Running HyperDbg has multiple stages. First, you should make sure to enable Intel **VT-x** from the BIOS. Next, you have to disable **driver signature enforcement** and turn off **Virtualization Based Security (VBS)**. Then you can run HyperDbg.

On the rest of this page, you'll read a detailed explanation about performing the above stages.

### Check for VMX support

If you've ever run HyperDbg and encountered the below error, it means that **VT-x** is disabled from your BIOS.

![Disabled VT-x From BIOS](../.gitbook/assets/vmx-is-not-supported.PNG)

Enabling **VT-x** from BIOS is vendor-specific. Usually, if you press \[**F2**], or \[**Delete**], or \[**ESC**] during the boot time, you'll enter the BIOS, and there should be an option to **Support Virtualization** or something like that. You should enable it from BIOS, and after that, you're good to go.&#x20;

### Disable Driver Signature Enforcement

The next step is disabling **Driver Signature Enforcement (DSE)**.

HyperDbg's driver is **NOT **digitally signed.

In order to disable **driver signature enforcement**, we have plenty of options. However, we recommend the **first option**, which is **attaching WinDbg at the boot time**. It's because this way, PatchGuard will not start, and some of HyperDbg's commands like the '[!syscall](https://docs.hyperdbg.org/commands/extension-commands/syscall)' or the '[!sysret](https://docs.hyperdbg.org/commands/extension-commands/sysret)', which are PatchGuard detectable, will be usable.&#x20;

If you use other options, please keep in mind that you should be cautious as PatchGuard will start and detect some of the modifications and might be problematic.

* Disable DSE by Attaching WinDbg (**Recommended**)
* Temporarily Disable DSE
* Putting Windows in Test Mode

#### Disable DSE by Attaching WinDbg

If you choose the first option, you can use **kdnet.exe** from Windows SDK, which is described [here](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/setting-up-a-network-debugging-connection-automatically). After that, you'll get a key that can be used on a remote machine to debug this machine using WinDbg.

![Kdnet.exe](../.gitbook/assets/configure-kdnet.PNG)

When you load the HyperDbg's driver, you can close the WinDbg, and everything is handled in HyperDbg, and no need for WinDbg anymore. We want WinDbg to avoid starting PatchGuard and let us load our unsigned driver.

If your computer has the secure boot enabled, you'll see the following error.

![Kdnet.exe (secure boot)](../.gitbook/assets/kdnet-secure-boot-error.PNG)

You can disable secure boot from the BIOS. Most of the time, you should change the **secure boot** option to "**Other OSes**" that are not Windows.

#### Temporarily Disable DSE

This is the simplest way to disable driver signature enforcement on Windows 10 but bear in mind that this method will temporarily disable driver signature enforcement.

After you restart, your computer driver signature enforcement will automatically turn itself on.

To disable driver signature enforcement, do the following:

1. Press and hold the **Shift** key on your keyboard and click the **Restart** button.
2. Choose to **Troubleshoot > Advanced options > Startup Settings** and click the **Restart** button.
3. When your computer restarts, you’ll see a list of options. Press **F7** on your keyboard to select **Disable driver signature enforcement**.
4. Your computer will now restart, and you’ll be able to install unsigned drivers.

Bear in mind that this method only temporarily disables driver signature enforcement, and after a restart, you have to re-disable it again.

#### Putting Windows in Test Mode

In test mode, you can install any drivers you want without experiencing any problems. Don’t forget to go to normal Windows 10 mode after you solve your problem:

1. Open an elevated command prompt window on your PC: right-click on the Windows Start icon and select **Command prompt (Admin)**.
2. In cmd type:`bcdedit /set TESTSIGNING ON`
3. Close the cmd window and restart your computer.
4. Install your drivers.

For more options, please visit [here](https://windowsreport.com/driver-signature-enforcement-windows-10/).

### Disable VBS, HVCI, and Device Guard

The last step before running HyperDbg is disabling **Virtualization Based Security (VBS)**.

**HyperDbg** and **VBS** are both hypervisors running on ring -1. These hypervisors are not compatible, and you should disable VBS (and its sub-components like HVCI, Device Guard, etc.).

To check whether VBS is running on your system, type **System Information** on the start menu and click on the **System Information** app. After that, check whether the VBS is **running** or not like the below picture.&#x20;



![VBS running](../.gitbook/assets/VBS-running.PNG)

If you see "**Enabled but not running**" or "**Not enabled**", you're good to go to the next step.

![Enabled but not running](../.gitbook/assets/VBS-enabled-but-not-running.PNG) ![Not enabled](../.gitbook/assets/VBS-not-enabled.PNG)

If the VBS is enabled, you can disable it by typing "**Core isolation**" on the start menu and turning off "**Memory integrity**".

![Turn off core isolation](../.gitbook/assets/Disable-core-isolation.PNG)

The above step is enough to disable the VBS. After that, you should restart your computer so that VBS will be disabled on the next start.

If the above method didn't work for you, open **Local Group Policy Editor (gpedit.msc)** and navigate to the following path:

```
Local Computer Policy\Computer Configuration\Administrative Templates\System\Device Guard
```

Click on **Turn on Virtualization Based Security**.

![](../.gitbook/assets/local-group-policy-VBS.PNG)

Then choose the '**Disabled**'** **option and click '**OK**'.

![](../.gitbook/assets/disable-VBS.PNG)

After that, you should restart your computer and recheck **System Information** to see if it's still running or not.

There are also other options to disable **VBS** as described [here](https://beebom.com/how-disable-virtualization-based-security-vbs-windows-11/).

## Run & Test

Congratulations, you're ready to run HyperDbg. Open **hyperdbg-cli.exe**, and visit [Quick Start](https://docs.hyperdbg.org/getting-started/quick-start) to start using **HyperDbg**.
