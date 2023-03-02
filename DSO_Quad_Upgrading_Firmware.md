---
title: DSO Quad:Upgrading Firmware
category: MakerPro
bzurl: 
oldwikiname:  DSO Quad:Upgrading Firmware
prodimagename:
surveyurl: https://www.research.net/r/DSO_Quad_Upgrading_Firmware
sku:
---
## Requirements

You will need the following:
Hardware
Mini USB Cable
PC
Software
Microsoft Windows (Mac and Linux are currently not supported for upgrading, but see below for Linux instructions)
Files
Latest firmware for your hardware version (see Firmware List)

## Upgrade Process

The device is connected to a PC and booted into upgrade mode. It is displayed by the host OS as a USB disk. A firmware file is copied to the device, and the device disconnects from the PC to prepare the file for installation. The device reconnects to accept another file till all files are copied. The device is then power cycled to complete the upgrade.

## Upgrade Instructions

### Windows

1. download the firmware
2. unpack/unzip the firmware
3. Power off the device and connect it to your PC with a Mini USB cable
4. Boot the device into upgrade mode by holding down the play/pause button (">||") while turning on the power
5. the DSO Quad/dso203 will be recognized as storage device

- Upgrade FPGA
  1. move the *.ADR to the storage device
  2. the storage device will disappear and shortly afterwords reappear
  3. move the *.BIN
  4. the storage device will disappear and shortly afterwords reappear
- Upgrade System
  1. move the *.hex to the storage device
  2. the storage device will disappear and shortly afterwords reappear
- Upgrade APP
  1. move the *.hex to the storage device
  2. the storage device will disappear and shortly afterwords reappear

When the storage device reppear, it will confirme the firmware upgrade with renaming the file extension to .rdy (meaning ready/succesfull) or .err (error).
If there is a *.ADR in the zip Archive allways start with the file. Do not drag and drop both files at the same time. Move the files one by one to your dso.

### Mac

Instructions to be added as support becomes available.

It should be noted that currently the DSO Quad and OS X do not play nicely with each other. As OS X tries to create hidden files such as .DS_Store, the DSO Quad creates .err files and because OS X transfers files along with additional hidden resources, each .BIN/.HEX will fail and the device will not update. The firmware mode directories should start out clean and empty or the DSO Quad will not update.

To clean this up if this happens to you, use OS X to remove all .err folders and files, then quickly disconnected the DSO Quad before OS X can create more hidden files.. This is done because Windows will refuse to remove these files as many of the err folders appear to be recursively referencing themselves.
Once this is done, you can connect the DSO Quad to a Windows PC and begin using it to transfer firmware updates.

### Linux

More instructions to be added as support becomes available.

1.Power off the device and connect it to your PC with a Mini USB cable
2.Boot the device into upgrade mode by holding down the play/pause button (">||") while turning on the power
3.Access terminal/shell prompt
4.Locate your correct DSO Quad device

```
dmesg | tail
```

5.You can see a text like it:

```
[20908.237181] scsi 57:0:0:0: Direct-Access     Vertual  DFU Disk              PQ: 0 ANSI: 2
[20908.243872] sd 57:0:0:0: Attached scsi generic sg1 type 0
[20908.250152] sd 57:0:0:0: [sdb] 1024 512-byte logical blocks: (524 kB/512 KiB)
[20908.254117] sd 57:0:0:0: [sdb] Write Protect is off
[20908.254130] sd 57:0:0:0: [sdb] Mode Sense: 03 00 00 00
[20908.254139] sd 57:0:0:0: [sdb] Assuming drive cache: write through
[20908.265144] sd 57:0:0:0: [sdb] Assuming drive cache: write through
[20908.289226]  sdb: unknown partition table
[20908.301166] sd 57:0:0:0: [sdb] Assuming drive cache: write through
[20908.301182] sd 57:0:0:0: [sdb] Attached SCSI removable disk
```

- Ignore partition error.
- Your correct device is inside brackets.

6.Mount it (replace sdb with sdc, sdd, etc according to the dmesg output).

```
mkdir -p /mnt/dso
mount -t vfat -o sync /dev/sdb /mnt/dso
```

7.Copy .bin/.hex/.adr file.

```
cp SYS_xxxx.HEX /mnt/dso/
```

8.Umount /mnt/dso and restart your DSO Quad (turn it off, turn it on again while holding ">||")

```
umount /mnt/dso
```

9. Repeat steps 7 and 8 for APP_xxx.HEX, CFG_FPGA.ADR and FPGA_Vxx.BIN files, on this order (one file at a time). The FPGA files are not always necessary.
10. Restore internal memory (2MB), download image file here: Dso.img.zip

```
bunzip2 Dso.img.zip --stdout | dd of=/dev/sdb
```

Step 10 is (as far as I know) the same as "you need to format the quad disk after upgrading" described below. And the DSO should be in the normal "on" mode (i.e. waveforms displayed) rather than upgrade mode, with the filesystem unmounted. The dd command will take a about 40 seconds, and when it is completed should output

```
4096+0 records in
4096+0 records out
2097152 bytes (2.1 MB) copied, 41.4337 s, 50.6 kB/s
```

## Troubleshooting

### IO Error on copying under Linux

I got this error while copying the SYS_XXX.HEX and APP_XXX.HEX:

```
cp: writing ”/mnt/dso/SYS_B150.HEX”: I/O-error
```

It was because of the sync option in the mount command. I used mount -t vfat /dev/sdb /mnt/dso and everything worked fine. Didn't even have to restart DSO between files, just unmount and remount.

### Minor upgrade problem in Linux

I've mounted with "mount /dev/sdc /mnt/sdo" (no options).
I've to rename files *.hex to *.HEX (uppercase) before upgrade.
Copied file an wait until "ls /mnt/dso" is empty, then umount.
SYS_B152.HEX
APP_B253.HEX
AP3_P100.HEX (this app start pressing APP3/Record button at boot)

### Your Issues Here

Please add issues you have with upgrading firmware to this list. Someone who knows how to solve the problem can then write a quick guide to resolution.

**ISSUE LIST:**

- DSO Quad will turn on a work normally with exception of wave generator. and pressing >|| while connected to the computer only opens up the drive for a portion of a second instead of keeping it open
- ...
- ...

## Firmware List

### Hardware V2.81

New FPGA chip is used, so FPGA configuration file has changed. SYS and APP are still compatible with HW2.7x version.

- [FPGA_281.zip](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/FPGA_281.zip)

### Hardware V2.72

Tip for version named "DS203" batch between May ~ Jun, 2013 : Two TVS static protection devices (0603ESDA-05) were added for FCC and CE compliance. They are clearly marked D2 and D3 for channel A and B under the shield. Those fuse at above 35V. If you try to calibrate with 50V, using the standard calibration utility, they might fire and cause the channel input to become short. In this case, it is recommended that you remove those devices: unsolder the shield in 3 spots, remove the surface mount components. (DSO Quad can forget about this tip)

- [DS203_V2.72_Schematic](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/DS203_V2.72_Schematic_20131106_.pdf)

| Release Date | SYS Version                                  | APP Version                                  | FPGA Version                                                                                    | Manual |  |
| ------------ | -------------------------------------------- | -------------------------------------------- | ----------------------------------------------------------------------------------------------- | ------ | - |
| 2013-07-19   | SYS_B160 SYS_V1.60_src.rar SYS_B161 SYS_B162 | Plus_A1_V1.10 Plus_A1_V1.10_src.rar PA1_V113 | [FPGA261](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/FPGA261.zip) | N/A    |  |

!!!note
    Internal flash memory size changed from 2M to 8M from HW version2.72. For this reason, you must use sys version B160 to drive the hardware correctly.

### Hardware V2.7/2.7B

- [DSO Quad V2.7 schematic;](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/DSO_Quad_V2.7_schematic.pdf)

Changes from v2.6 (found so far...)

| Component             | Net       | Alteration       |
| --------------------- | --------- | ---------------- |
| R5,R6,R5A,R6A,R3A,R4A |           | Value change     |
| C9,C10,C11,C12        |           | Value change     |
| L2,L3,L10             |           | Remove           |
| D9,LCM                |           | Component change |
| U20                   |           | New component    |
|                       | VBO → VB | Name change      |

| Release Date |                                           SYS Version                                           |                                                                                                                                                    APP Version                                                                                                                                                    | FPGA Version |                                                          Manual                                                          |
| :----------: | :----------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------: | :----------: | :----------------------------------------------------------------------------------------------------------------------: |
| 2012-07-011 | [Sys_152](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/SYS_B152.rar) | [App_100](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/AP1_P100.rar) `<br>`[App_103](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/AP3_P100.rar) `<br>`[App_107](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/PA1_V107.rar) |      -      | [User manual](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/DS203_yijian_app_user_manual.rar) |

### Hardware V2.6

- [DSO Quad V2.6 schematic]()| Release Date | SYS Version                                                                                      | APP Version                                                                                                    | FPGA Version                                                                                       | Manual                                                                                                                           | Notes                                                                                                           |
  | ------------ | ------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
  | 2011-04-19   | [sys_134](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/SYS_B134.rar) | [app_232](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/APP_B232.rar)               | [FPGA_222](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/FPGA_V222.rar) | N/A                                                                                                                              | N/A                                                                                                             |
  | 2011-05-18   | -                                                                                                | [app_235](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/APP_B235%20(1).rar)         | [FPGA_25](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/FPGA_V25.rar)   | N/A                                                                                                                              | No need to update the sys                                                                                       |
  | 2011-06-01   | [sys_137](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/SYS_B137.rar) | -                                                                                                              | -                                                                                                  | [Manual 092b](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/DSO%20Quad%20v2%206%20Manual%200.92b.rar) | N/A                                                                                                             |
  | 2011-06-07   | [sys_141](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/sys141.rar)   | [app_243](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/App_V243.rar)               | -                                                                                                  | -                                                                                                                                | App_243 must be installed with sys_141 or above                                                                 |
  | 2011-06-16   | -                                                                                                | [app_245](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/APP_V245.rar)               | -                                                                                                  | -                                                                                                                                | -                                                                                                               |
  | 2011-06-17   | -                                                                                                | [app_245(b)](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/APP_V245%2528b%2529.rar) | -                                                                                                  | -                                                                                                                                | -                                                                                                               |
  | 2011-07-11   | [Sys_150](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/SYS_B150.zip) | [app_250](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/APP_B250.zip)               | -                                                                                                  | -                                                                                                                                | you need to format the Quad disk after upgrading,ref http://www.seeedstudio.com/forum/viewtopic.php?f=22&t=2112 |
  | 2011-11-15   | [Sys_151](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/SYS_B151.zip) | [App_252](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/AP1_B252.zip)               | [FPGA261](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/FPGA261.zip)    | -                                                                                                                                | the Sys151 and app252 must be upgraded with the FPGA261                                                         |
  | 2012-02-14   | -                                                                                                | [AP3_P100](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/AP3_P100.rar)              | -                                                                                                  | -                                                                                                                                | UI Improvement                                                                                                  |
  | 2012-04-16   | [Sys_152](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/SYS_B152.rar) | [App_253](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/APP_B253.zip)               | -                                                                                                  | -                                                                                                                                | -                                                                                                               |

Some versions of the firmware are not released in a single file, you will have to grab them from previous versions.

### Hardware V2.2

| Release Date | SYS Version | APP Version | FPGA Version | Notes                  |  |
| ------------ | ----------- | ----------- | ------------ | ---------------------- | - |
| 2011-01-06   | 12.29       | 01.06       | 0108         | Shows as 1.02 and 2.01 |  |

## DFU Hex/Binaries

Can be used when your DSO's bricked.

- [DFU_C312-SYS_B160-FPGA_V261-APP_PA1_V111_LOGO-Seeed - HW2.72, -HW2.7B(need downgrading the sys hex to B152)](https://github.com/SeeedDocument/DSO_Quad_Upgrading_Firmware/raw/master/res/DFU_C312-SYS_B160-FPGA_V261-APP_PA1_V111_LOGO-Seeed(BC3B).hex.zip)
- DFU_C311 - HW2.7B
- DFU_C310 - HW2.6 (white screen when dfu mode but works fine)

How to flash the dfu? Please refer to this thread of forum: http://www.seeedstudio.com/forum/viewtopic.php?f=26&t=4628&start=0
Note: when the dfu's reflashed, your dso will engage a license error issue and the logo image will lost. But all these don't affect the usage. If you really matter the license error issue, please feedback via our tech support channel attaching your serial number for re-generating a license code.
