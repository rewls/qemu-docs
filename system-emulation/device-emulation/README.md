# Device Emulation

- QEMU supports the emulation of a large number of devices from peripherals such network cards and USB devices to integrated systems on a chip (SoCs).

- Configuration of these is often a source of confusion so it helps to have an understanding of some of the terms used to describes devices within QEMU.

## Common Terms

### Device Front End

- A device front end is how a device is presented to the guest.

- The type of device presented should match the hardware that the guest operating system is expecting to see.

- All devices can be specified with the `-device` command line option.

- Running QEMU with the command line options `-device help` will list all devices it is aware of.

- Using the command line `-device foo,help` will list the additional configuration options available for that device.

<br>

- A front end is often paired with a back end, which describes how the host’s resources are used in the emulation.

### Device Buses

- Most devices will exist on a BUS of some sort.

- Depending on the machine model you choose (`-M foo`) a number of buses will have been automatically created.

- In most cases the BUS a device is attached to can be inferred, for example PCI devices are generally automatically allocated to the next free address of first PCI bus found.

### Device Back End

- The back end describes how the data from the emulated device will be processed by QEMU.

- The configuration of the back end is usually specific to the class of device being emulated.

<br>

- While the choice of back end is generally transparent to the guest, there are cases where features will not be reported to the guest if the back end is unable to support it.

### Device Pass Through

- Device pass through is where the device is actually given access to the underlying hardware.

- This can be as simple as exposing a single USB device on the host system to the guest or dedicating a video card in a PCI slot to the exclusive use of the guest.
