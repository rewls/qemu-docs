# About QEMU

- QEMU is a generic and open source machine emulator and virtualizer.

<br>

- QEMU can be used in several different ways.

- The most common is for System Emulation, where it provides a virtual model of an entire machine (CPU, memory and emulated devices) to run a guest OS.

- In this mode the CPU may be fully emulated, or it may work with a hypervisor such as KVM, Xen or Hypervisor.

- Framework to allow the guest to run directly on the host CPU.

<br>

- The second supported way to use QEMU is User Mode Emulation, where QEMU can launch processes compiled for one CPU on another CPU.

- In this mode the CPU is always emulated.

- QEMU also provides a number of standalone command line utilities, such as the `qemu-img` disk image utility that allows you to create, convert and modify disk images.
