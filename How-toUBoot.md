# How-to: Build U-Boot Image

- [How-to: Build U-Boot Image](#how-to-build-u-boot-image)
  - [1. Prepare the compile environment](#1-prepare-the-compile-environment)
  - [2. Prepare FPGA project folder](#2-prepare-fpga-project-folder)
  - [3. Retrieve U-Boot source](#3-retrieve-u-boot-source)
  - [4. Apply handoff files to the U-Boot source code](#4-apply-handoff-files-to-the-u-boot-source-code)
  - [5. Configure and build U-Boot:](#5-configure-and-build-u-boot)
  - [6. Prepare boot configuration files](#6-prepare-boot-configuration-files)


## 1. Prepare the compile environment
Before build the U-Boot image for ADRV9002 RF-SoM, you need to prepare FPGA handoff files.  
You need Quartus Prime 20.1.1 Standard Edition and Soc EDS 20.1 Standard Edition.  

To compile Linux kernel for ADRV9002 RF-SoM, we use [Arm GNU Toolchain](https://developer.arm.com/Tools%20and%20Software/GNU%20Toolchain), formerly known as [GNU Arm Embedded Toolchain](https://developer.arm.com/downloads/-/gnu-rm).

```Shell
$ wget https://developer.arm.com/-/media/Files/downloads/gnu-a/10.2-2020.11/binrel/gcc-arm-10.2-2020.11-x86_64-arm-none-linux-gnueabihf.tar.xz
$ tar xf gcc-arm-10.2-2020.11-x86_64-arm-none-linux-gnueabihf.tar.xz
$ export TOP_FOLDER=`pwd`
$ export PATH=$TOP_FOLDER/gcc-arm-10.2-2020.11-x86_64-arm-none-linux-gnueabihf/bin:$PATH
```

Also, you have to install some packages for dependency.

```shell
$ sudo apt install bc bison build-essential \
  device-tree-compiler dfu-util efitools flex gdisk graphviz imagemagick \
  liblz4-tool libgnutls28-dev libguestfs-tools libncurses-dev \
  libpython3-dev libsdl2-dev libssl-dev lz4 lzma lzma-alone openssl \
  pkg-config python3 python3-asteval python3-coverage python3-filelock \
  python3-pkg-resources python3-pycryptodome python3-pyelftools \
  python3-pytest python3-pytest-xdist python3-sphinxcontrib.apidoc \
  python3-sphinx-rtd-theme python3-subunit python3-testtools \
  python3-virtualenv swig uuid-dev
```




## 2. Prepare FPGA project folder
Before building U-Boot, FPGA design has to be completed.  
In the FPGA design folder, you can see handoff folder (in ADRV9002 RF-SoM project, folder name is "hps_isw_handoff").  
Following steps is performed in FPGA folder.

```Shell
$ export PROJECT_FOLDER=`/path/to/FPGA/project/folder/`
# for example, project folder name is "yonaguni_lvds_linux" and located in $TOP_FOLDER/,
# export PROJECT_FOLDER=$TOP_FOLDER/yonaguni_lvds_linux
```

Make sure to regenerate handoff folder if you modify FPGA designs.  
For further information, please refer Intel's documents.

Run embedded_command_shell.sh included in SoC EDS, then run bsp-create-settings with no user options to convert the handoff data into source code:
```Shell
cd $PROJECT_FOLDER
mkdir -p software/bootloader
cd software/bootloader
# run next command in embedded_command_shell.sh environment
#  for example, SoC EDS folder name is "intelFPGA/20.1/
$ ./intleFPGA/20.1/embedded/embedded_command_shell.sh
$ bsp-create-settings \
   --type spl \
   --bsp-dir software/bootloader \
   --preloader-settings-dir "hps_isw_handoff/system_bd_hps_0" \
   --settings software/bootloader/settings.bsp
```


## 3. Retrieve U-Boot source

```Shell
cd $PROJECT_FOLDER/software/bootloader
$ git clone https://github.com/altera-opensource/u-boot-socfpga
$ cd u-boot-socfpga
# use next line to use the 2020.07 U-Boot branch
$ git checkout -b test-bootloader -t origin/socfpga_v2020.07
# If doing the same thing as the above three lines in one line,
# git clone -b socfpga_v2020.07 https://github.com/altera-opensource/u-boot-socfpga u-boot-socfpga
```
Latest officialy supported u-boot for Quartus Standard v20.1.1 is u-boot-socfpga v2021.04, but we use u-boot-socfpga v2020.07 in this procedure.  
We only checked with u-boot-socfpga v2020.07.


## 4. Apply handoff files to the U-Boot source code
Run the qts_filer script to take the sources from the handoff folder, format them appropriately and copy them to the U-Boot source code:
```Shell
$ cd $PROJECT_FOLDER/software/bootloader/u-boot-socfpga
$ ./arch/arm/mach-socfpga/qts-filter.sh \
   cyclone5 \
   ../../../ \
   ../ \
   ./board/altera/cyclone5-socdk/qts/
# above line is the same as the next comment:
# ./arch/arm/mach-socfpga/qts-filter.sh cyclone5 $PROJECT_FOLDER $PROJECT_FOLDER/software/bootloader ./board/altera/cyclone5-socdk/qts/
```
Refer to U-Boot documentation for more details about the qts-filter script: https://github.com/altera-opensource/u-boot-socfpga/blob/socfpga_v2020.07/doc/README.socfpga


## 5. Configure and build U-Boot:
`-j` option is used for multithread compiling.
```Shell
$ cd $PROJECT_FOLDER/software/bootloader/u-boot-socfpga
$ export CROSS_COMPILE=arm-none-linux-gnueabihf-
$ export UBOOT_OUTDIR=../u-boot_out
$ make socfpga_cyclone5_defconfig O=$UBOOT_OUTDIR
$ make menuconfig O=$UBOOT_OUTDIR
$ make LOCALVERSION= O=$UBOOT_OUTDIR -j 8
$ make tools O=$UBOOT_OUTDIR -j 8
```


## 6. Prepare boot configuration files
```Shell
$ cd $UBOOT_OUTDIR
$ vim u-boot.txt
```
Write below lines to u-boot.txt:
```Shell
fatload mmc 0:1 $loadaddr yonaguni_cmos.rbf;
fpga load 0 $loadaddr $filesize;
setenv fdtfile yonaguni_cmos.dtb;
bridge enable;
```
Convert u-boot.txt to u-boot script.
```Shell
$ tools/mkimage -A arm -O linux -T script -C none -a 0 -e 0 -n "FPGA_config" -d u-boot.txt u-boot.scr
```

Next, prepare extlinux.conf:
```Shell
$ mkdir extlinux
$ vim extlinux/extlinux.conf
```
Write below lines to extlinux/extlinux.conf:
```Shell
LABEL Linux Default
    KERNEL ../zImage
    FDT ../yonaguni_cmos.dtb
    APPEND root=/dev/mmcblk0p2 rw rootwait earlyprintk console=ttyS0,115200n8
```
