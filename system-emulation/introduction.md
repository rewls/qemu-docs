# Introduction

## Virtualisation Accelerators

- QEMU's system emulation provides a virtual model of a machine (CPU, memory and emulated devices) to run a guest OS.

- It supports a number of hypervisors (known as accelerators) as well as a JIT known as the Tiny Code Generator (TCG) capable of emulating many CPUs.

## Feature Overview

- System emulation provides a wide range of device models to emulate various hardware components you may want to add to your machine.

- This includes a wide number of VirtIO devices which are specifically tuned for efficient operation under virtualisation.

- Some of the device emulation can be offloaded from the main QEMU process using either vhost-user (for VirtIO) or Multi-process QEMU.

- If the platform supports it QEMU also supports directly passing devices through to guest VMs to eliminate the device emulation overhead.

- See Device Emulation for more details.

<br>

- There is a full featured block layer which allows for construction of complex storage topology which can be stacked across multiple layers supporting redirection, networking, snapshots and migration support.

<br>

- The flexible `chardev` system allows for handling IO from character like devices using stdio, files, unix sockets and TCP networking.

<br>

- QEMU provides a number of management interfaces including a line based Human Monitor Protocol (HMP) that allows you to dynamically add and remove devices as well as introspect the system state.

- The QEMU Monitor Protocol (QMP) is a well defined, versioned, machine usable API that presents a rich interface to other tools to create, control and manage Virtual Machines.

- This is the interface used by higher level tools interfaces such as Virt Manager using the libvirt framework.

<br>

- For the common accelerators QEMU, supported debugging with its `gdbstub` which allows users to connect GDB and debug system software images.

## Running

- QEMU provides a rich and complex API which can be overwhelming to understand.

- While some architectures can boot something with just a disk image, those examples elide a lot of details with defaults that may not be optimal for modern systems.

<br>

- For a non-x86 system where we emulate a broad range of machine types, the command lines are generally more explicit in defining the machine and boot behaviour.

- You will find often find example command lines in the QEMU System Emulator Targets section of the manual.

<br>

- While the project doesn’t want to discourage users from using the command line to launch VMs, we do want to highlight that there are a number of projects dedicated to providing a more user friendly experience.

- Those built around the libvirt framework can make use of feature probing to build modern VM images tailored to run on the hardware you have.

<br>

- That said, the general form of a QEMU command line can be expressed as:

    ```sh
    $ qemu-system-x86_64 [machine opts] \
                    [cpu opts] \
                    [accelerator opts] \
                    [device opts] \
                    [backend opts] \
                    [interface opts] \
                    [boot opts]
    ```

- Most options will generate some help information.

- So for example:

    ```sh
    $ qemu-system-x86_64 -M help
    ```

    - will list the machine types supported by that QEMU binary. 

- `help` can also be passed as an argument to another option.

- For example:

    ```sh
    $ qemu-system-x86_64 -device scsi-hd,help
    ```

    - will list the arguments and their default values of additional options that can control the behaviour of the scsi-hd device.

### Options Overview

|Options|Description|
|-|-|
|Machine|Define the machine type, amount of memory etc|
|CPU|Type and number/topology of vCPUs. Most accelerators offer a host cpu option which simply passes through your host CPU configuration without filtering out any features.|
|Accelerator|This will depend on the hypervisor you run. Note that the default is TCG, which is purely emulated, so you must specify an accelerator type to take advantage of hardware virtualization.|
|Devices|Additional devices that are not defined by default with the machine type.|
|Backends|Backends are how QEMU deals with the guest’s data, for example how a block device is stored, how network devices see the network or how a serial device is directed to the outside world.|
|Interfaces|How the system is displayed, how it is managed and controlled or debugged.|
|Boot|How the system boots, via firmware or direct kernel boot.|

- In the following example we first define a virt machine which is a general purpose platform for running Aarch64 guests.

- We enable virtualisation so we can use KVM inside the emulated guest. As the virt machine comes with some built in pflash devices we give them names so we can override the defaults later.

    ```sh
    $ qemu-system-aarch64 \
       -machine type=virt,virtualization=on,pflash0=rom,pflash1=efivars \
       -m 4096 \
    ```

- We then define the 4 vCPUs using the max option which gives us all the Arm features QEMU is capable of emulating.

- We enable a more emulation friendly implementation of Arm’s pointer authentication algorithm.

- We explicitly specify TCG acceleration even though QEMU would default to it anyway.

    ```sh
    -cpu max,pauth-impdef=on \
    -smp 4 \
    -accel tcg \
    ```

- As the virt platform doesn’t have any default network or storage devices we need to define them.

- We give them ids so we can link them with the backend later on.

    ```sh
    -device virtio-net-pci,netdev=unet \
    -device virtio-scsi-pci \
    -device scsi-hd,drive=hd \
    ```

- We connect the user-mode networking to our network device.

- As user-mode networking isn’t directly accessible from the outside world we forward localhost port 2222 to the ssh port on the guest.

    ```sh
    -netdev user,id=unet,hostfwd=tcp::2222-:22 \
    ```

- We connect the guest visible block device to an LVM partition we have set aside for our guest.

    ```sh
    -blockdev driver=raw,node-name=hd,file.driver=host_device,file.filename=/dev/lvm-disk/debian-bullseye-arm64 \
    ```

- We then tell QEMU to multiplex the QEMU Monitor with the serial port output (we can switch between the two using Keys in the character backend multiplexer).

- As there is no default graphical device we disable the display as we can work entirely in the terminal.

    ```sh
    -serial mon:stdio \
    -di splay none \
    ```

- Finally we override the default firmware to ensure we have some storage for EFI to persist its configuration.

- That firmware is responsible for finding the disk, booting grub and eventually running our system.

    ```sh
    -blockdev node-name=rom,driver=file,filename=(pwd)/pc-bios/edk2-aarch64-code.fd,read-only=true \
    -blockdev node-name=efivars,driver=file,filename=$HOME/images/qemu-arm64-efivars
    ```
