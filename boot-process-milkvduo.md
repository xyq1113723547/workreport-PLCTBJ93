# Milk-V Duo boot components 启动组件
# FSBL
- https://github.com/milkv-duo/duo-buildroot-sdk/tree/develop/fsbl
  - FSBL stands for First Stage Boot Loader. Act as ATF (Arm Trusted Firmware) BL2 (Bootloader 2).
  - FSBL 代表第一阶段引导加载程序。 充当 ATF（Arm 可信固件）BL2（引导加载程序 2）.
  - In prebuild form (以预构建形式).
- compiled object file 编译后的目标文件:
  - https://github.com/milkv-duo/duo-buildroot-sdk/tree/develop/fsbl/plat/cv180x/bl2_objs/cv1800b_sophpi_duo_sd/bl2
  - https://github.com/milkv-duo/duo-buildroot-sdk/tree/develop/fsbl/plat/cv180x/prebuilt/bl31.bin
- Unlikely to upstream, since it's contains binary blobs and adapted from Arm.
- 不太可能上游，因为它包含二进制文件并改编自 Arm

# OpenSBI
- https://github.com/milkv-duo/duo-buildroot-sdk/tree/develop/opensbi
- fork from T-head opensbi https://github.com/T-head-Semi/opensbi
- 未 upstream

# U-boot
- https://github.com/milkv-duo/duo-buildroot-sdk/tree/develop/u-boot-2021.10
- fork from https://github.com/u-boot/u-boot/commit/d80bb749fab53da72c4a0e09b8c2d2aaa3103c91
- 未 upstream

# fip.bin
- Include FSBL OpenSBI u-boot
- [fip.bin 编译流程](https://zhuanlan.zhihu.com/p/657030662)
- 由于原生u-boot编译出u-boot.bin 无法直接刻录到FLASH中。我们采取ARM Trusted Firmware Design中的Firmware Image Package (FIP)方式，将uboot.bin封装在fip.bin里面，而fip_spl.bin是引导快启过程中加载的镜像的文件。
https://doc.sophgo.com/cvitek-develop-docs/master/docs_latest_release/CV180x_CV181x/zh/01.software/OSDRV/U-boot_Porting_Development_Guide/build/html/3_U-boot_Transplant.html
- ![Layout of FIP.bin](https://doc.sophgo.com/cvitek-develop-docs/master/docs_latest_release/CV180x_CV181x/zh/01.software/OSDRV/Secure_Boot_User_Guide/build/html/_images/image1.jpg)
- ![burning boot process](https://doc.sophgo.com/cvitek-develop-docs/master/docs_latest_release/CV180x_CV181x/zh/01.software/OSDRV/Cvitek_Bare_and_Non-Bare_Chip_Burning_Upgrade_Operation_Guide/build/html/_images/image1.png)

## Status and proposal for downstream Linux boot adaptation 针对下游 Linux 启动的现状和适配提议
- FSBL 源码仓库包含有二进制，不太可能推到上游，且 fip.bin 的结构和编译流程较为复杂，加载 fip.bin 的程序位于更底层的 Bootrom，没有相应的文档和源码。
- Milkv-Duo SDK 编译后的 Linux 内核经过包装成为了 boot.sd, 没有相应文档。
- 根据社区用户的帖子[使用uboot引导自己的操作系统](https://community.milkv.io/t/uboot/181)，通过修改 U-boot 的配置，重新编译生成 fip.bin, 默认 kernel.bin 在 FAT32 格式的 boot 分区内，加载后通过指令 go 来跳转。
- 启动固件不开源是常有的事，比如树莓派系列。在下游的 Linux 发行版中，需要固件才能启动的例子也很多。通常固件放在 boot 分区，默认他们能工作，能执行到 bootloader 即可。

## Yocto 能在启动组件做些什么?
- 能编译 U-boot OpenSBI
- 通过设置额外的 layer，也能实现编译 FSBL，如在 [meta-xilinx zynq](https://xilinx-wiki.atlassian.net/wiki/spaces/A/pages/18841671/Using+meta-xilinx-tools+layer)，但修改较为复杂
