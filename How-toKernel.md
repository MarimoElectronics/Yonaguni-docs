# How-to: Build Yonaguni Kernel Image

- [How-to: Build Yonaguni Kernel Image](#how-to-build-yonaguni-kernel-image)
  - [1. Prepare the compile environment](#1-prepare-the-compile-environment)
  - [2. Get kernel source](#2-get-kernel-source)
  - [3. Apply modifications](#3-apply-modifications)
  - [4. Configure and build](#4-configure-and-build)


## 1. Prepare the compile environment
To compile Linux kernel for Yonaguni, we use [Arm GNU Toolchain](https://developer.arm.com/Tools%20and%20Software/GNU%20Toolchain).

```Shell
$ wget https://developer.arm.com/-/media/Files/downloads/gnu/11.2-2022.02/binrel/gcc-arm-11.2-2022.02-x86_64-arm-none-linux-gnueabihf.tar.xz
$ tar xf gcc-arm-11.2-2022.02-x86_64-arm-none-linux-gnueabihf.tar.xz
$ export TOP_FOLDER=`pwd`
$ export ARCH=arm
$ export CROSS_COMPILE=$TOP_FOLDER/gcc-arm-11.2-2022.02-x86_64-arm-none-linux-gnueabihf/bin/arm-none-linux-gnueabihf-
$ export KERNEL_OUTDIR $TOP_FOLDER/kernel_out
```

Also, you have to install some packages for dependency.

```shell
$ sudo apt install gawk wget git-core diffstat unzip texinfo \
  gcc-multilib  build-essential chrpath socat cpio python python3 \
  python3-pip python3-pexpect  xz-utils debianutils iputils-ping \
  python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev  pylint3 xterm \
  libncurses-dev flex bison openssl libssl-dev m4 libgmp3-dev libmpc-dev \
  libmpfr-dev
```


## 2. Get kernel source

```Shell
$ git clone -b 2021_r2 https://www.github.com/MarimoElectronics/linux
```

Please make sure to sync the branch to the SD card image release you will use.  
> e.g.  
> SD card image: Kuiper Linux 2021_R2  
> --> Kernel branch: 2021_r2  
> --> HDL branch: hdl_2021_r2  


## 3. Apply modifications
MarimoElectronics/linux repository is already applied modifications to analogdevicesinc/linux for Yonaguni reference design.  
If you prefer to keep no identifiers such as `-dirty` or `+` in the kernel version string, you must keep it locally committed and turn off the option in the next step.


Modified files list:
```
firmware/Navassa_CMOS_profile.json
firmware_Navassa_LVDS_profile.json
```
Device Tree file modification is not needed at this time, but have to be completed before preparing SD card image.


## 4. Configure and build
`-j` option is used for multithread compiling.

```Shell
$ make socfpga_adi_defconfig O=$KERNEL_OUTDIR
$ make menuconfig O=$KERNEL_OUTDIR
# Uncheck [General setup > Automatically append version information to the version string]
$ make zImage LOCALVERSION= O=$KERNEL_OUTDIR -j8
# Leave LOCALVERSION blank to match the kernel version string to the Kuiper Linux release.
```
