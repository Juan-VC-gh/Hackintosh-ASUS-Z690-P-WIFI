Alder Lake Hackintosh!

Specs:

Motherboard: ASUS PRIME Z690-P WIFI

Processor: Intel Core i7-12700K

GPU: MSI AMD RX 6600 XT MECH 2X -> Intel's 12th gen iGPU is not supported as there are no real Macs with any Alder Lake CPU hence no iGPU drivers. Some Navi 2 GPU drivers were added on macOS 11.4 like RX 6800, RX 6800 XT, RX 6900 XT while for RX 6600 and RX 6600 XT drivers were added on macOS 12.1, thus minimum install version for this setup

SSDs: 2x WD_BLACK SN850 1TB

RAM: 2x Kingston Fury Beast 16GB DDR5 5200 MHz

WIFI + BT: Fenvi T919 -> Packs a BCM94360CD which is natively supported (the chipset is used in real Macs) for seamlessly support

What works?
- Everything I can think of (Wifi + BT + sleep + DRM content + Apple services + jack audio + DP/HDMI audio, etc) except digital on-board audio output (custom AppleALC codec layout id is needed for this) -> ALC 897

Short-guide (for experimented users):

1. Update the UEFI to v2014 (released on Oct 19, 2022). The BIOS update file can be found inside the UEFI Updates directory or download directly from [ASUS](https://dlcdnets.asus.com/pub/ASUS/mb/BIOS/PRIME-Z690-P-WIFI-ASUS-2014.zip). Boot into UEFI and load default (or optimized settings as it is shown)

2. In the UEFI GUI navigate to 
    * Advanced -> System Agent (SA) Configuration -> Graphics Configuration -> set Primary Display: Auto and iGPU Multi-monitor: Enabled (required)
    * Also within the UEFI, enable XMP to take max advantage of the RAM (optional)

3. Using macOS terminal:
	``` 
	diskutil list
	diskutil partitionDisk /dev/diskU GPT JHFS+ "USB" 100%
	sudo "/Applications/Install macOS Monterey.app/Contents/Resources/createinstallmedia" --volume /Volumes/USB 
	```
	**Note:** Replace **U** in /dev/disk**U** with the USB disk number

4. Copy EFI folder to the EFI partition of the USB

5. Boot OpenCore from the USB pressing "F8" when the system starts

6. ~~In OpenCore menu press space bar, scroll & open CFGLock.efi tool~~   -> CFGLock variable seems to be missing from the firmware past UEFI v1603 and CPU register MSR 0xE2 can be written in order to achieve native PM (power management)

7. ~~It will find the CFGLock variable offset, accept to toggle its value to turn it off as it cannot be changed from the firmware settings~~   -> CFGLock variable seems to be missing from the firmware past UEFI v1603 and CPU register MSR 0xE2 can be written in order to achieve native PM (power management)

8. Boot the installer and proceed normally as a Mac following the instructions

9. When you finally boot macOS make sure to setup configure correctly the unique values each mac comes to access Apple services like AppStore, FaceTime, iMessage, etc -> This is usually found on the internet like "hackintosh fix apple services" or similar

10. In macOS, go to System Preferences -> Energy Saver -> In the Power Section **ONLY** have enabled "Put hard disks to sleep when possible". This is because **"Wake for network access" causes sleep issues** and the other options disabled are optional but it is better that way for power saving

10. To make the setup USB-independent, in macOS terminal:
	```
	diskutil list
	sudo diskutil mount /dev/diskUsV
	```   
	**Note:** Replace **U** with the disk number and **V** with the partition identifier of the system's EFI partition in /dev/disk**U**s**V**

11. Copy the BOOT and OC directories from the repo into the EFI directory from the system partition

12. Create a boot-entry that points to OpenCore.efi -> This can be achieved using [DiskGenius](https://www.diskgenius.com/how-to/manage-uefi-boot-options.php)
	
	**Tip:** Within the OpenCore boot menu move with the arrow keys and highlight your preferred entry to boot by default and then set it by holding the 
	ctrl key and then press enter

13. Enjoy macOS on non-Apple hardware :)
