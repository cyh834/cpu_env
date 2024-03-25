FROM ubuntu:22.04

# Update apt software source (for China mainland):
RUN sed -i s:/archive.ubuntu.com:/mirrors.tuna.tsinghua.edu.cn:g /etc/apt/sources.list

# Prerequisites
RUN apt-get clean \
    && apt-get -y update --fix-missing \
    && apt-get install -y gcc-riscv64-linux-gnu make gcc vim \
    && apt-get install -y build-essential automake libtool texinfo bison flex gawk g++ git xxd curl wget gdisk gperf cpio bc screen texinfo unzip libgmp-dev libmpfr-dev libmpc-dev libssl-dev libncurses-dev libglib2.0-dev libpixman-1-dev libyaml-dev patchutils python3-pip zlib1g-dev device-tree-compiler dosfstools mtools kpartx rsync

# VisionFive2
RUN mkdir ~/VisionFive2 

# U-Boot
RUN cd ~/VisionFive2  \
    && git clone https://github.com/starfive-tech/u-boot.git \
    && export U_BOOT_PATH=$(pwd)/u-boot \
    && cd u-boot 
    ## && git checkout -b JH7110_VisionFive2_devel origin/JH7110_VisionFive2_devel \
    ## && git pull \ 
    ## && make starfive_visionfive2_defconfig ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- \
    ## && make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- \

# OpenSBI
RUN cd ~/VisionFive2  \
    && git clone https://github.com/starfive-tech/opensbi.git \
    && cd opensbi 
    ## && make ARCH=riscv CROSS_COMPILE=riscv64-linux-gnu- PLATFORM=generic FW_PAYLOAD_PATH=$U_BOOT_PATH/u-boot.bin FW_FDT_PATH=$U_BOOT_PATH/arch/riscv/dts/starfive_visionfive2.dtb FW_TEXT_START=0x40000000 

# SPL
RUN cd ~/VisionFive2  \
    && git clone https://github.com/starfive-tech/Tools \
    && cd Tools 
    ## && git checkout master \
    ## && git pull \
    ## && cd spl_tool/ \
    ## && make \
    ## && ./spl_tool -c -f $U_BOOT_PATH/spl/u-boot-spl.bin \

# Linux Kernel
RUN cd ~/VisionFive2  \
    && git clone https://github.com/starfive-tech/linux.git \
    && cd linux 
    ## && git checkout -b JH7110_VisionFive2_devel origin/JH7110_VisionFive2_devel \
    ## && git pull \
    ## && make starfive_visionfive2_defconfig CROSS_COMPILE=riscv64-linux-gnu- ARCH=riscv \
    ## && make CROSS_COMPILE=riscv64-linux-gnu- ARCH=riscv menuconfig \
    ## && make CROSS_COMPILE=riscv64-linux-gnu- ARCH=riscv -jx 

# Rootfs
RUN cd ~/VisionFive2 \
    && mkdir rootfs \
    && cd rootfs \
    && mkdir dev usr bin sbin lib etc proc tmp sys var root mnt \
    && git clone https://git.busybox.net/busybox \
    && cd busybox
    ## make CROSS_COMPILE=riscv64-linux-gnu- ARCH=riscv menuconfig

# our-os
RUN mkdir ~/our-os \
    && cd ~/our-os \
    && git clone https://github.com/u-boot/u-boot 

RUN cd ~/our-os \
    && git clone https://github.com/riscv-software-src/opensbi 
RUN cd ~/our-os \
    && git clone https://github.com/torvalds/linux --depth 1 -b v5.15-rc3
RUN cd ~/our-os \
    && git clone https://git.busybox.net/busybox 

## RUN cd ~/our-os/linux \
##     && git fetch --unshallow 

# Finish
RUN cd ~


