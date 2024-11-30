# Arm System emulator

- QEMU can emulate both 32-bit and 64-bit Arm CPUs.

- Use the `qemu-system-aarch64` executable to simulate a 64-bit Arm machine.

- You can use either `qemu-system-arm` or `qemu-system-aarch64` to simulate a 32-bit Arm machine: in general, command lines that work for `qemu-system-arm` will behave the same when used with `qemu-system-aarch64`.

<br>

- QEMU has generally good support for Arm guests.

- It has support for nearly fifty different machines.

- The reason we support so many is that Arm hardware is much more widely varying than x86 hardware.

- Arm CPUs are generally built into “system-on-chip” (SoC) designs created by many different companies with different devices, and these SoCs are then built into machines which can vary still further even if they use the same SoC.

- Even with fifty boards QEMU does not cover more than a small fraction of the Arm hardware ecosystem.

<br>

- The situation for 64-bit Arm is fairly similar, except that we don’t implement so many different machines.

<br>

- As well as the more common "A-profile" CPUs (which have MMUs and will run Linux) QEMU also supports "M-profile" CPUs such as the Cortex-M0, Cortex-M4 and Cortex-M33 (which are microcontrollers used in very embedded boards).

- For most boards the CPU type is fixed (matching what the hardware has), so typically you don’t need to specify the CPU type by hand, except for special cases like the virt board.

## Choosing a board model

- For QEMU’s Arm system emulation, you must specify which board model you want to use with the `-M` or `-machine` option; there is no default.

<br>

- Because Arm systems differ so much and in fundamental ways, typically operating system or firmware images intended to run on one machine will not run at all on any other.

<br>

- If you already have a system image or a kernel that works on hardware and you want to boot with QEMU, check whether QEMU lists that machine in its `-machine help` output.

- If it is listed, then you can probably use that board model.

- If it is not listed, then unfortunately your image will almost certainly not boot on QEMU.

<br>

- If you don’t care about reproducing the idiosyncrasies of a particular bit of hardware, such as small amount of RAM, no PCI or other hard disk, etc., and just want to run Linux, the best option is to use the `virt` board.

- This is a platform which doesn’t correspond to any real hardware and is designed for use in virtual machines.

- You’ll need to compile Linux with a suitable configuration for running on the `virt` board.

- `virt` supports PCI, virtio, recent CPUs and large amounts of RAM.

- It also supports 64-bit CPUs.

## Board-specific documentation

### Raspberry Pi boards (`raspi0`, `raspi1ap`, `raspi2b`, `raspi3ap`, `raspi3b`, `raspi4b`)

- QEMU provides models of the following Raspberry Pi boards:

> ##### `raspi0` and `raspi1ap`

- ARM1176JZF-S core, 512 MiB of RAM

#### Implemented devices

- ARM1176JZF-S, Cortex-A7, Cortex-A53 or Cortex-A72 CPU

- Interrupt controller

- DMA controller

- Clock and reset controller (CPRMAN)

- System Timer

- GPIO controller

- Serial ports (BCM2835 AUX - 16550 based - and PL011)

- Random Number Generator (RNG)

- Frame Buffer

- USB host (USBH)

- SD/MMC host controller

- SoC thermal sensor

- USB2 host controller (DWC2 and MPHI)

- MailBox controller (MBOX)

- VideoCore firmware (property)

- Peripheral SPI controller (SPI)

- Broadcom Serial Controller (I2C)

#### Missing devices

- Pulse Width Modulation (PWM)
