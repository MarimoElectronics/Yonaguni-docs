# How-to: Make ADRV9002 RF-SoM SD Card Image

- [How-to: Make ADRV9002 RF-SoM SD Card Image](#how-to-make-adrv9002-rf-som-sd-card-image)
  - [1. Download Analog Devices Kuiper Linux](#1-download-analog-devices-kuiper-linux)
  - [2. Prepare Need files](#2-prepare-need-files)
  - [3. Replace boot files](#3-replace-boot-files)
  - [Conclusion](#conclusion)


## 1. Download Analog Devices Kuiper Linux
Go [Kuiper Linux wiki](https://wiki.analog.com/resources/tools-software/linux-software/adi-kuiper_images/release_notes) and download actual zip file.  
If the supported OS version by ADRV9002 RF-SoM is older than latest release, expand **Old Releases** block and find direct link.  
Please be sure to verify the checksum for the downloaded image.  
Then unzip file.


## 2. Prepare Need files
Prepare files listed below:

```
extlinux/extlinux.conf
u-boot-with-spl.sfp
u-boot.scr
./device_tree/yonaguni_cmos.dtb
yonaguni_cmos.rbf
zImage
```

If you want to prepare above files from source, please refer each instructions.  
[How-to: Customize and Build FPGA Project](https://github.com/MarimoElectronics/Yonaguni-FPGA/blob/main/How-toFPGA.md), to prepare yonaguni_cmos.rbf. If your rbf file has a different name, rename to "yonaguni_cmos.rbf" or modify settings in [How-to: Build U-boot](./How-toUBoot.md)  
[How-to: Build U-boot](./How-toUBoot.md), to prepare extlinux/extlinux.conf, u-boot-with-spl.sfp, u-boot.scr  
[How-to: Build Linux Kernel Image](./How-toKernel.md), to prepare zImage


Note:
yonaguni_cmos.dtb is the Device Tree blob file.  
For ADRV9002 RF-SoM, we provide two Device Tree blob files:  
| Filename | ADRV9002 Operation Mode |
----|----
| yonaguni_cmos.dtb | independent mode |
| yonaguni_cmos-rx2tx2.dtb | MIMO mode |

These dtb file is in "device_tree" directory in this repository.  
If you want to use MIMO mode device tree, rename to "yonaguni_cmos.dtb"  
For further information about ADRV9002 operation mode, please refer:  
https://wiki.analog.com/resources/tools-software/linux-drivers/iio-transceiver/adrv9002#device_modes

If you want to use MIMO mode device tree, please rename "yonaguni_cmos-rx2tx2.dtb" to "yonaguni_cmos.dtb" then place to boot partition in the SD card image.  


## 3. Replace boot files
In this instruction, we use kpartx to mount `.img` file without writing to SD card.  

```Shell
$ sudo apt install kpartx
  :
$ sudo kpartx -av 2023-04-02-ADI-Kuiper-full.img
add map loop0p1 (252:0): 0 4194304 linear 7:0 24576
add map loop0p2 (252:1): 0 20447232 linear 7:0 4218880
add map loop0p3 (252:2): 0 16384 linear 7:0 8192
$ ls /dev/mapper/
control  loop0p1  loop0p2  loop0p3
```
Note: The "0" after the "loop" above can be a different number.

For Kuiper Linux image, 1st partition is for `/boot`, 2nd partition is for `/`(rootfs), and the last partition is for the boot image partition (custom partition type=0xA2).  

1. Mount the boot partition to a certain mount point.

    ```Shell
    $ sudo mkdir /sdroot
    $ sudo mount -t vfat -o loop /dev/mapper/loop0p1 /sdroot/
    $ ls /sdroot
    COPYING.linux             fixup4cd.dat                           zynq-adrv9361-z7035-fmc                            zynq-zed-adv7511-ad7768-1-evb
    LICENCE.broadcom          fixup4db.dat                           zynq-adrv9361-z7035-packrf                         zynq-zed-adv7511-ad7768-4-axi-adc
    README.txt                fixup4x.dat                            zynq-adrv9364-z7020-bob                            zynq-zed-adv7511-ad7768-axi-adc
    VERSION.txt               fixup_cd.dat                           zynq-adrv9364-z7020-packrf                         zynq-zed-adv7511-ad936x-fmcomms2-3-4
    bcm2708-rpi-b-plus.dtb    fixup_db.dat                           zynq-common                                        zynq-zed-adv7511-ad9467-fmc-250ebz
    bcm2708-rpi-b-rev1.dtb    fixup_x.dat                            zynq-coraz7s-cn0501                                zynq-zed-adv7511-adaq8092
    bcm2708-rpi-b.dtb         issue.txt                              zynq-coraz7s-cn0540                                zynq-zed-adv7511-adrv9002
    bcm2708-rpi-cm.dtb        kernel.img                             zynq-coraz7s-cn0579_i2c                            zynq-zed-adv7511-cn0363
    bcm2708-rpi-zero-w.dtb    kernel7.img                            zynq-zc702-adv7511                                 zynq-zed-adv7511-cn0506-mii
    bcm2708-rpi-zero.dtb      kernel7l.img                           zynq-zc702-adv7511-ad9361-fmcomms5                 zynq-zed-adv7511-cn0506-rgmii
    bcm2709-rpi-2-b.dtb       kuiper.json                            zynq-zc702-adv7511-ad936x-fmcomms2-3-4             zynq-zed-adv7511-cn0506-rmii
    bcm2710-rpi-2-b.dtb       overlays                               zynq-zc706-adv7511                                 zynq-zed-adv7511-cn0577
    bcm2710-rpi-3-b-plus.dtb  rpi_git_properties.txt                 zynq-zc706-adv7511-ad6676-fmc                      zynq-zed-imageon
    bcm2710-rpi-3-b.dtb       socfpga_arria10_common                 zynq-zc706-adv7511-ad9081                          zynq-zed-otg
    bcm2710-rpi-cm3.dtb       socfpga_arria10_socdk_ad9081           zynq-zc706-adv7511-ad9082                          zynqmp-adrv9009-zu11eg-revb-adrv2crr-fmc-revb
    bcm2710-rpi-zero-2-w.dtb  socfpga_arria10_socdk_ad9136_fmc_ebz   zynq-zc706-adv7511-ad9172-fmc-ebz                  zynqmp-adrv9009-zu11eg-revb-adrv2crr-fmc-revb-fmcbridge
    bcm2710-rpi-zero-2.dtb    socfpga_arria10_socdk_adrv9002         zynq-zc706-adv7511-ad9265-fmc-125ebz               zynqmp-adrv9009-zu11eg-revb-adrv2crr-fmc-revb-sync-fmcomms8
    bcm2711-rpi-4-b.dtb       socfpga_arria10_socdk_adrv9009         zynq-zc706-adv7511-ad9361-fmcomms5                 zynqmp-adrv9009-zu11eg-revb-adrv2crr-fmc-revb-xmicrowave
    bcm2711-rpi-400.dtb       socfpga_arria10_socdk_adrv9371         zynq-zc706-adv7511-ad9361-fmcomms5-ext-lo-adf5355  zynqmp-common
    bcm2711-rpi-cm4.dtb       socfpga_arria10_socdk_daq2             zynq-zc706-adv7511-ad936x-fmcomms2-3-4             zynqmp-zcu102-rev10-ad9081
    bcm2835-rpi-a-plus.dtb    socfpga_arria10_socdk_fmcomms8         zynq-zc706-adv7511-ad9434-fmc-500ebz               zynqmp-zcu102-rev10-ad9082-m4-l8
    bcm2835-rpi-a.dtb         socfpga_cyclone5_common                zynq-zc706-adv7511-ad9625-fmcadc2                  zynqmp-zcu102-rev10-ad9083-fmc-ebz
    bcm2835-rpi-b-plus.dtb    socfpga_cyclone5_de10_nano_cn0540      zynq-zc706-adv7511-ad9625-fmcadc3                  zynqmp-zcu102-rev10-ad9172-fmc-ebz-mode4
    bcm2835-rpi-b-rev2.dtb    socfpga_cyclone5_de10_nano_cn0579_i2c  zynq-zc706-adv7511-ad9739a-fmc                     zynqmp-zcu102-rev10-ad9361-fmcomms5
    bcm2835-rpi-b.dtb         socfpga_cyclone5_de10_nano_hps         zynq-zc706-adv7511-adrv9002                        zynqmp-zcu102-rev10-ad936x-fmcomms2-3-4
    bcm2835-rpi-cm1-io1.dtb   socfpga_cyclone5_sockit_arradio        zynq-zc706-adv7511-adrv9008-1-2                    zynqmp-zcu102-rev10-ad9695
    bcm2835-rpi-zero-w.dtb    start.elf                              zynq-zc706-adv7511-adrv9009                        zynqmp-zcu102-rev10-ad9783
    bcm2835-rpi-zero.dtb      start4.elf                             zynq-zc706-adv7511-adrv937x                        zynqmp-zcu102-rev10-adrv9002
    bcm2836-rpi-2-b.dtb       start4cd.elf                           zynq-zc706-adv7511-cn0506-mii                      zynqmp-zcu102-rev10-adrv9008-1-2
    bcm2837-rpi-3-a-plus.dtb  start4db.elf                           zynq-zc706-adv7511-cn0506-rgmii                    zynqmp-zcu102-rev10-adrv9009
    bcm2837-rpi-3-b-plus.dtb  start4x.elf                            zynq-zc706-adv7511-cn0506-rmii                     zynqmp-zcu102-rev10-adrv9009-fmcomms8
    bcm2837-rpi-3-b.dtb       start_cd.elf                           zynq-zc706-adv7511-fmcdaq2                         zynqmp-zcu102-rev10-adrv937x
    bcm2837-rpi-cm3-io3.dtb   start_db.elf                           zynq-zc706-adv7511-fmcdaq3-revC                    zynqmp-zcu102-rev10-cn0506-mii
    bootcode.bin              start_x.elf                            zynq-zc706-adv7511-fmcjesdadc1                     zynqmp-zcu102-rev10-cn0506-rgmii
    cmdline.txt               uEnv.txt                               zynq-zc706-adv7511-fmcomms11                       zynqmp-zcu102-rev10-cn0506-rmii
    config.txt                versal-common                          zynq-zed-adv7511                                   zynqmp-zcu102-rev10-fmcdaq2
    fixup.dat                 versal-vck190-reva-ad9081              zynq-zed-adv7511-ad4020                            zynqmp-zcu102-rev10-fmcdaq3
    fixup4.dat                zynq-adrv9361-z7035-bob                zynq-zed-adv7511-ad4630-24                         zynqmp-zcu102-rev10-stingray
    ```

2. Remove all files from BOOT partition and write all prepared ADRV9002 RF-SoM boot files.

    ```Shell
    $ sudo rm -rf /sdroot/
    $ sudo mkdir /sdroot/extlinux
    $ sudo cp --preserve=timestamps extlinux/extlinux.conf /sdroot/extlinux
    $ sudo cp --preserve=timestamps u-boot-with-spl.sfp /sdroot
    $ sudo cp --preserve=timestamps u-boot.scr /sdroot
    $ sudo cp --preserve=timestamps ./device_tree/yonaguni_cmos.dtb /sdroot
    $ sudo cp --preserve=timestamps yonaguni_cmos.rbf /sdroot
    $ sudo cp --preserve=timestamps zImage /sdroot
    ```

3. Unmount the boot partition and write `u-boot-with-spl.sfp` to the boot image partition.

    ```Shell
    $ sudo umount /sdroot
    $ sudo dd if=u-boot-with-spl.sfp of=/dev/mapper/loop0p3
      :
    ```

4. Cleanup the device map.

    ```Shell
    $ sudo kpartx -d 2023-04-02-ADI-Kuiper-full.img
    $ sudo rm -r /sdroot
    ```

If you'd like to customize the rootfs, mount loop0p2 and execute `chroot`.


## Conclusion
Now you have the ADRV9002 RF-SoM SD card image.  
Write the image to a SD card.

The following is an example where the SD card drive is sda.

   ```Shell
    $ sudo dd 2023-04-02-ADI-Kuiper-full.img of=/dev/sda status=progress
   ```


Note:  
If you want to apply above boot files / kernel modifications after writing image to SD card, you can simply place or replace files to the appropriate partitions in the SD card.
