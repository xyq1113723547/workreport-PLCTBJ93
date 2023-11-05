# Yocto

## 官方上游仓库 PR

https://github.com/riscv/meta-riscv/pull/437

## MACHINE
添加 `conf/machine/milkv-duo.conf`  
其中包含了
- 机器默认特性 `MACHINE_FEATURES`
- kernel 的类型和镜像文件类型 `KERNEL_CLASSES` `KERNEL_IMAGETYPE`
- 串口配置 `SERIAL_CONSOLES`
- 系统镜像文件类型 `IMAGE_FSTYPES`
- 镜像启动所需文件 `IMAGE_BOOT_FILES`
- WKS 镜像文件 `WKS_FILE`

## Boot
编写配置了 `milkv-duo-bootfiles.bb`, 用于在生成系统镜像之前, 添加官方 SDK 编译好的 fip.bin 到 boot 分区.  
参考了 RT-Thread 启动模式 https://github.com/RT-Thread/rt-thread/tree/master/bsp/cv1800b


# Linux kernel
## 背景
官方适配 Milk-V Duo 的 Linux 5.10 内核目前位于 [SDK](https://github.com/milkv-duo/duo-buildroot-sdk/tree/develop/linux_5.10)  
另外有官方团队正在于 Linux 上游添加 Milk-V Duo 的适配: https://lore.kernel.org/lkml/20230930123937.1551-1-jszhang@kernel.org/T/#t
## 问题
Yocto 的工作流程在 kernel 配置检测时, 因为 `Kconfiglib` 文件夹是下游所独有, 会报错.  
因为上游 RISC-V ISA 的改动, 而需要在内核编译添加 `TOOLCHAIN_NEEDS_EXPLICIT_ZICSR_ZIFENCEI` 选项, 以确保工具链需要配置才带有 `Zicsr` 和 `Zifencei` 的指令.  
以及 Milk-V Duo 启动固件所需的设备树文件 .dts, .dtsi, .h 皆在内核源码之外, 无法整合到 Yocto 的内核编译流程
## 解决方案
个人创建了基于 [cvitek-linux-5.10](https://github.com/milk-v/cvitek-linux-5.10) 的 fork.  
https://github.com/xyq1113723547/cvitek-linux-5.10
解决了上述的问题, 同时规范了 `Kconfig` `Makefile` 在 RISC-V 基础和扩展指令上的命名和配置, 匹配了上游工具链的定义, 默认编译不带有任何版本的 Vector 指令, 暂时取消了 t-head 独有的指令和下游独有的 `CFLAGS=-mno-ldd` 编译选项.  
Yocto 上的 kernel 配置 `linux-milkv-duo-dev.bb` 则结合了 `allwinner`, `starfive` 等友商的 Yocto 上游配置, 配置了基于 Linux 5.10 的 fork 版本, 通过添加的脚本指令来实现自定义 level 的 LZMA 内核镜像压缩, 设备树的编译和打包, 通过 SDK 已有的 `multi.its` 生成 `kernel.itb (boot.sd)`, 以匹配固件中 U-Boot 的 image tree 格式.


 ## 个人公开相关软件仓库

 - [my linux 5.10 dev fork](https://github.com/xyq1113723547/cvitek-linux-5.10)
 - [meta-riscv](https://github.com/xyq1113723547/meta-riscv/tree/milkv-duo)
 - [milkv duo bootfiles](https://github.com/xyq1113723547/milkv-duo-bootfiles)
 

 ## 个人公开技术文档和工作周总结
 - [boot process on Milk-V Duo](https://github.com/xyq1113723547/workreport-PLCTBJ93/blob/main/boot-process-milkvduo.md)
 - [device tree on Milk-V Duo](https://github.com/xyq1113723547/workreport-PLCTBJ93/blob/main/device_tree.md)
 - [week1&2_en.md](week1&2_en.md)
 - [week3.md](week3.md)

