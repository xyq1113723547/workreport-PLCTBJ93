# FAQ

## What can be built by using Yocto?
- custom Linux distrobution (Linux system image including kernel, bootloader, libraries, and applications)
- SDK (https://docs.yoctoproject.org/sdk-manual/index.html)
- firmware (bootloaders, like u-boot and grub, linux-firmware)
- user applications

## How to help Milk-V Duo / CV1800b enter Yocto mainline in general?
- Yocto doesn't support particular machine by default
- Need to add machine configuration, modifications, and applications by BSP layer ([Board Support Packages Developer’s Guide](https://docs.yoctoproject.org/bsp-guide/bsp.html))
- [meta-riscv](https://github.com/riscv/meta-riscv) is a layer considered upstream by [riscv.org](riscv.org)

# Technical trials and errors
## Build kernel only
- without adding MACHINE variable and machine configuration files
- just changed the SRI_URI to use [cvitek-linux-5.10](https://github.com/milk-v/cvitek-linux-5.10), which is the kernel source code repo from milk-v
- *FAILED* since virtual/kernel need to be fulfilled and satified by COMPATIBLE_MACHINE


## set COMPATIBLE_MACHINE to qemurisc64
- *FAILED* during kernel unpack stage, should not happen nevertheless
- error logs (*TODO*)

## Reference to add machine to Yocto
- read [allwinnerd1 conf in meta-riscv](https://github.com/riscv/meta-riscv/blob/master/conf/machine/nezha-allwinner-d1.conf)
- read [BSP guide](https://docs.yoctoproject.org/bsp-guide/index.html)
- read [meta-raspberrypi](https://github.com/agherzan/meta-raspberrypi)

## Files I add
- create `linux-milkv-duo-dev.bb`, which provides `virtual/kernel` by `PREFERRED_PROVIDER_virtual/kernel = "linux-milkv-duo-dev"`
- 
