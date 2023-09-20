# Kernel for Milk-V Duo
- [cvitek-linux-5.10](https://github.com/milk-v/cvitek-linux-5.10)
- [my fork for cvitek-linux-5.10](https://github.com/xyq1113723547/cvitek-linux-5.10)

## my modifications
- delete extra downstream folder called Kconfiglib
  - It contains python scripts to reimplement Kconfig functions, like defconfig, saveconfig, menuconfig. it's unnecessary and causes error for yocto task `do_kernel_configcheck`
  - Error log:
  - ```
    ERROR: linux-milkv-duo-dev-5.10.4+git-r0 do_kernel_configcheck: config analysis failed when running 'symbol_why.py --dotconfig /home/yanqi/riscv-yocto/build/tmp-glibc/work/milkv_duo-oe-linux/linux-milkv-duo-dev/5.10.4+git/linux-milkv_duo-standard-build/.config --mismatches --classify': 
    ERROR: Logfile of failure stored in: /home/yanqi/riscv-yocto/build/tmp-glibc/work/milkv_duo-oe-linux/linux-milkv-duo-dev/5.10.4+git/temp/log.do_kernel_configcheck.75671
    ERROR: Task (/home/yanqi/riscv-yocto/meta-riscv/recipes-kernel/linux/linux-milkv-duo-dev.bb:do_kernel_configcheck) failed with exit code '1'
    ```
    `symbol_why.py` in Ycoto would assume that the Kconfiglib in kernel source is a file rather than a folder.
- comment out `-mno-ldd`
  - This kernel compile option is a downstream flag from T-head SDK and Sophgo(算能) inherits it. Not upstreamed and no documentation.
  - The likely reason is that ldd has performance issue in T-head C906.
  - `objdump -p /path/to/program | grep NEEDED` is recommended as a safer alternative in manpage of [ldd](https://man7.org/linux/man-pages/man1/ldd.1.html).
 
- Add kernel options
  - `TOOLCHAIN_NEEDS_EXPLICIT_ZICSR_ZIFENCEI`
