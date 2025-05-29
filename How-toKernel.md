# How-to: Build ADRV9002 RF-SoM Kernel Image

- [How-to: Build ADRV9002 RF-SoM Kernel Image](#how-to-build-adrv9002-rf-som-kernel-image)
  - [1. Prepare the compile environment](#1-prepare-the-compile-environment)
  - [2. Get kernel source](#2-get-kernel-source)
  - [3. Apply modifications](#3-apply-modifications)
  - [4. Configure and build](#4-configure-and-build)


## 1. Prepare the compile environment
To compile Linux kernel for ADRV9002 RF-SoM, we use [Arm GNU Toolchain](https://developer.arm.com/Tools%20and%20Software/GNU%20Toolchain), formerly known as [GNU Arm Embedded Toolchain](https://developer.arm.com/downloads/-/gnu-rm).

```Shell
$ wget https://developer.arm.com/-/media/Files/downloads/gnu-a/10.2-2020.11/binrel/gcc-arm-10.2-2020.11-x86_64-arm-none-linux-gnueabihf.tar.xz
$ tar xf gcc-arm-10.2-2020.11-x86_64-arm-none-linux-gnueabihf.tar.xz
$ export TOP_FOLDER=`pwd`
$ export ARCH=arm
$ export CROSS_COMPILE=$TOP_FOLDER/gcc-arm-10.2-2020.11-x86_64-arm-none-linux-gnueabihf/bin/arm-none-linux-gnueabihf-
$ export KERNEL_OUTDIR=$TOP_FOLDER/kernel_out
```

Also, you have to install some packages for dependency.

```shell
$ sudo apt install gawk wget git-core diffstat unzip texinfo \
  gcc-multilib  build-essential chrpath socat cpio python3 \
  python3-pip python3-pexpect  xz-utils debianutils iputils-ping \
  python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev xterm \
  libncurses-dev flex bison openssl libssl-dev m4 libgmp3-dev libmpc-dev \
  libmpfr-dev
```
Note: The above dependencies are for Ubuntu 22.04 and may depend on the OS version.


## 2. Get kernel source

```Shell
$ git clone -b 2023_r2_p1 https://www.github.com/analogdevicesinc/linux
```

Please make sure to sync the branch to the SD card image release you will use.  
> e.g.  
> SD card image: Kuiper Linux 2023_r2 Patch1  
> --> Kernel branch: 2023_r2_p1  
> --> HDL branch: 2023_R2_p1  


## 3. Apply modifications
Apply modifications to analogdevicesinc/linux for ADRV9002 RF-SoM reference design.  

Modified files list:
```
firmware/Navassa_CMOS_profile.json
firmware/Navassa_LVDS_profile.json
```

```Shell
$ cd linux/firmware
$ vim Navassa_CMOS_profile.json
```

Change the "deviceClock_kHz" in the following lines from "38400" to "49152".
```
{
  "clocks": {
    "deviceClock_kHz": 38400,
    "clkPllVcoFreq_daHz": 884736000,
    "clkPllHsDiv": 0,
       :
```
Next, make the same changes to "Navassa_LVDS_profile.json".

Device Tree file modification is not needed at this time, but have to be completed before preparing SD card image.


## 4. Configure and build
`-j` option is used for multithread compiling.

```Shell
$ cd $TOP_FOLDER/linux
$ make socfpga_adi_defconfig O=$KERNEL_OUTDIR
$ make menuconfig O=$KERNEL_OUTDIR
# Uncheck [General setup > Automatically append version information to the version string]
$ make zImage LOCALVERSION= O=$KERNEL_OUTDIR -j8
# Leave LOCALVERSION blank to match the kernel version string to the Kuiper Linux release.
```

The zImage will be created in the following derectory.
```
./kernel_out/arch/arm/boot
```
