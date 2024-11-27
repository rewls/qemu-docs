# Invocation

```sh
qemu-system-x86_64 [options] [disk_image]
```

- `disk_image` is a raw hard disk image for IDE hard disk 0.

- Some targets do not need a disk image.

- When dealing with options parameters as arbitrary strings containing commas, such as in "file=my,file" and "string=a,b", it’s necessary to double the commas.

- For instance, "-fw_cfg name=z,string=a,,b" will be parsed as "-fw_cfg name=z,string=a,b".

## Standard options

> ##### `-h`

- Display help and exit

> ##### `-version`

- Display version information and exit

> ##### `-machine [type=]name[,prop=value[,...]]`

- Select the emulated machine by name.

- Use `-machine help` to list available machines.

> ##### `-cpu model`

- Select CPU model (`-cpu help` for list and additional feature selection)

> ##### `-m [size=]megs[,slots=n,maxmem=size]`

- Sets guest startup RAM size to megs megabytes.

- Default is 128 MiB.

- Optionally, a suffix of “M” or “G” can be used to signify a value in megabytes or gigabytes respectively.

### Block device options

- The QEMU block device handling options have a long history and have gone through several iterations as the feature set and complexity of the block layer have grown.

- Many online guides to QEMU often reference older and deprecated options, which can lead to confusion.

<br>

- The most explicit way to describe disks is to use a combination of `-device` to specify the hardware device and `-blockdev` to describe the backend.

- The device defines what the guest sees and the backend describes how QEMU handles the data.

- It is the only guaranteed stable interface for describing block devices and as such is recommended for management tools and scripting.

<br>

- The `-drive` option combines the device and backend into a single command line option which is a more human friendly.

- There is however no interface stability guarantee although some older board models still need updating to work with the modern blockdev forms.

<br>

- Older options like `-hda` are essentially macros which expand into -drive options for various drive interfaces.

- The original forms bake in a lot of assumptions from the days when QEMU was emulating a legacy PC, they are not recommended for modern configurations.

> ##### `-sd file`

- Use file as SecureDigital card image.

### Boot Image or Kernel specific

- There are broadly 4 ways you can boot a system with QEMU.

    - specify a firmware and let it control finding a kernel

    - specify a firmware and pass a hint to the kernel to boot

    - direct kernel image boot

    - manually load files into the guest’s address space

- The third method is useful for quickly testing kernels but as there is no firmware to pass configuration information to the kernel the hardware must either be probeable, the kernel built for the exact configuration or passed some configuration data (e.g. a DTB blob) which tells the kernel what drivers it needs.

- This exact details are often hardware specific.

<br>

- The final method is the most generic way of loading images into the guest address space and used mostly for bare metal type development where the reset vectors of the processor are taken into account.

<br>

- For x86 machines and some other architectures `-bios` will generally do the right thing with whatever it is given.

- For other machines the more strict `-pflash` option needs an image that is sized for the flash device for the given machine type.

<br>

- Please see the QEMU System Emulator Targets section of the manual for more detailed documentation.

> ##### `-kernel bzImage`

- Use bzImage as kernel image.

- The kernel can be either a Linux kernel or in multiboot format.

> ##### `-append cmdline`

- Use cmdline as kernel command line

> ##### `-dtb file`

- Use file as a device tree binary (dtb) image and pass it to the kernel on boot.
