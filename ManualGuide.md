# Manual Guide

## Requirements
- [DPCIManager](https://raw.githubusercontent.com/XLNCs/MacAUT/master/DPCIManager_v1.5(256).app.zip)
- [MaciASL](https://raw.githubusercontent.com/XLNCs/MacAUT/master/MaciASL_vRM1.31(252.4).app.zip)
- [Clover Configurator](https://raw.githubusercontent.com/XLNCs/MacAUT/master/CloverConfigurator_GE_v5.2.0.1.zip)

## Step 1 : Deleting old USB kexts

- Mount your macOS Volume EFI partition using [Terminal](https://youtu.be/DIvzVjuz13Q) or [Clover configurator](https://youtu.be/DIvzVjuz13Q).
- Navigate to **`EFI/EFI/CLOVER/kexts/`** folder and search all the sub-folders for these below listed kexts and delete them, if they exist.

  * **`GenericUSBXHCI.kext`**
  * **`DummyUSBXHCIPCI.kext`**
  * **`DummyUSBEHCIPCI.kext`**
  * **`USBInjectAll.kext`**
  
- Navigate to **`/System/Library/Extensions/`** folder and delete the above listed kexts, if they exist.
- Navigate to **`/Library/System/`** folder and delete the above listed kexts, if they exist.
- Now rebuild your kernel cache by running the below commands in terminal.
```bash
### Rebuildcache
sudo chown -R 0:0 /System/Library/Extensions/
sudo chmod -R 755 /System/Library/Extensions/
sudo touch /System/Library/Extensions/
rm -rf /System/Library/PrelinkedKernels/pre*
sudo kextcache -u /
```

## Step 2 : Extracting OEM DSDT

#### > Using Clover Bootloader Menu [RECOMENDED] :

 - While in **Clover Boot Menu** press **`F4`** on your keyboard to dump OEM DSDT.
 - Boot to macOS and mount your macOS Volume EFI partition.
 - Navigate to **`EFI/EFI/CLOVER/ACPI/origin/`** folder and copy the **`DSDT.aml`** file to your Desktop.

#### > Using DPCIManager App :

 - Open **DPCIManager.app** and click **`Extract DSDT`** button on the top left corner.
 - And save the **`DSDT.aml`** file on your Desktop.

## Step 3 : Patching the DSDT

- Open **MaciASL.app** and from the menu bar open Prefrences window by navigating to **`MaciASL -> Preferences`**.
- On preferences window click on **Sources** Tab and hit the **`+`** button to add a repo and type the following lines into the blank space and when done close that preferences window.
```bash
Name : RyzenUSB
URL  : http://raw.github.com/AlGreyy/Ryzen-USB-fix-/master
```
- From menu bar navigate to **`File -> Open`** and select your **`DSDT.aml`** file which is on your Desktop and open it.
- Now click on **`Patch`** button on the top middle to open patch window.
- Scroll down on the left navigation pane and find the listed **`RyzenUSB`** entry and expand it and select **`Ryzen USB FIX`** patch.
- Then click on Apply to apply the patch to **`DSDT.aml`**
- Now from the menu bar navigate to **`File -> Save`** to save the changes.
- Done, you can now close the App.

## Step 4 : Placing the patched DSDT in CLOVER.

- Make sure your macOS Volume EFI partition is mounted 
- Copy the **`DSDT.aml`** from your Desktop and paste it into to **`EFI/EFI/CLOVER/ACPI/patched/`** folder.

## Step 5 : Adding patches to Clover Config
- Open **`CloverConfigurator.app`** and from menu bar navigate to **`File -> Open`** and select your **`config.plist`** which is present in **`EFI/EFI/CLOVER/`** folder.
- Click on **`Kernel And Kexts Patches`** section on Clover Configurator window and under **`KextToPatch`** box add these following patches.

```bash
## For macOS 10.13 / 10.13.1 / 10.13.2 / 10.13.3
    Name             Find               Replace
AppleUSBXHCI    21F281FA 000002     21F281FA 000011
AppleUSBXHCI    D1000000 83F901     D1000000 83F910
AppleUSBXHCI    83BD7CFF FFFF0F     83BD7CFF FFFF1F

## For macOS 10.13.4 / 10.13.5 / 10.13.6 
    Name             Find               Replace
AppleUSBXHCI    C8000000 83FB02      C8000000 83FB11       

```
- When done , save the changes by navigating to **`File -> Save`** from menu bar.

 ----------
### Now reboot the system for changes to take effect.
