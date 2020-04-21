# Hackintosh steps for HP laptop
## Installation
1. Format USB drive to ```MacOS journaled``` format. Burn Olarila image using Balena Etcher.
2. Boot installer from USB. This laptop needs ```config2.plist``` and Intel graphics injection.
3. Start installer and format HDD to ```APFS format``` using disk utility. Then install MacOS.

## Post-installation
1. Boot into system using USB drive's bootloader.
2. Go to Olarila drive>Files folder. Run ```disablehibernate``` and ```master-disable```.
3. Bootloader fix
    1. Mount EFI partition.
        ```
        sudo diskutil mount EFI
        ```
    2. Download correct Clover version for chipset series from Olarila site. Extract it into EFI.

        https://www.olarila.com/topic/5676-folders-for-all-chipsets-clover-and-opencore/

    3. Find instructions for ```SSDT-EC.aml```, ```SSDT-OLARILA.aml``` and ```sdt.aml``` on internet. Paste them into ```EFI/CLOVER/ACPI/patched```.

        https://www.olarila.com/topic/5693-guide-ssdt-with-pikes-pm-script-and-use-with-cpufriend/


### Fine tuning
1. **DSDT**: Submit DSDT request on Olarila forums. Paste ```DSDT.aml``` in ```EFI/CLOVER/ACPI/patched```.

    https://www.olarila.com/topic/923-dsdt-patch-requests

*Fixes sound and microphone*

2. **Wifi**: Install driver for Railink USB dongle. **Don't install any other driver**.

    https://github.com/chris1111/Wireless-Ralink-Panel-Utility

3. **Intel Graphics and brightness**: Download correct ```.plist``` from Olarila site(Intel 520). Replace existing file in EFI drive with this one. This fixes brightness and brightness keys as well.

    https://www.olarila.com/topic/6420-guide-mobile-intel-hd-graphics/


4. **Embedded Controller(EC) fix for Catalina**: ACPI (Advanced Configuration and Power Interface) embedded controller handles power management.
    1. Go to Clover bootloader and press ```F4``` to generate system's ```DSDT.aml``` file at ```CLOVER/ACPI/ORIGIN```. This is different from patched DSDT at ```CLOVER/ACPI/PATCHED```.
    2. Install x for DSDT debugging.
    3. Open DSDT file and search for ```PNP0C09```. This is a field for either ```EC0```, ```H_EC``` or ```ECDV```.
    4. Open ```config.plist``` with Clover configurator. Go to ```Acpi>DSDT``` and enable option for the appropriate device name found in DSDT file(EC0 in this case).

*This probably fixed brightness and sound hotkeys*.

5. **Battery**: Requires DSDT patching.
    1. Install ```VirtualSMC and SMCBatteryManager``` kexts or ```FakeSMC and ACPIBatteryManager``` kexts. Do not mix these together.
    2. Open ```DSDT.aml``` with **MaciASL**.
    3. Find battery patch for HP laptop which gives least or no errors.

        https://github.com/RehabMan/Laptop-DSDT-Patch/blob/master/battery/battery_HP-Pavilion-n012tx.txt

    4. Fix errors caused due to missing variables. The patches replace variables higher than 8 bit with 8 bit ones. Eg. ```MBRM``` becomes ```BRM0``` and ```BRM1```. The patches have a ```B1B2()``` method which splits a 16 bit variable into an 8 bit one. Make substitutions for all missing variables in following manner:
        ```
        B1B2(^^LPCB.EC0.BRM0,^^LPCB.EC0.BRM1)
        B1B2(^^PCI0.LPCB.EC0.CUR0,^^PCI0.LPCB.EC0.CUR1)
        B1B2(^^PCI0.LPCB.EC0.BRM0,^^PCI0.LPCB.EC0.BRM1)
        B1B2(^^PCI0.LPCB.EC0.BCV0,^^PCI0.LPCB.EC0.BCV1)
        ```

6. **Bluetooth**: Natively works
7. **NTFS write**: Install mounty ```brew cask install mounty```.
8. **App store**: Works
