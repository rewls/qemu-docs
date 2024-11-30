# USB emulation

- QEMU can emulate a PCI UHCI, OHCI, EHCI or XHCI USB controller.

- You can plug virtual USB devices or real host USB devices (only works with certain host operating systems).

- QEMU will automatically create and connect virtual USB hubs as necessary to connect multiple USB devices.

## Connecting USB devices

- USB devices can be connected with the `-device usb-...` command line option or the `device_add` monitor command.

- Available devices are:

> ##### `usb-net[,netdev=id]`

- Network adapter that supports CDC ethernet and RNDIS protocols.

- `id` specifies a netdev defined with -netdev …,id=id. For instance, user-mode networking can be used with

```sh
$ qemu-system-x86_64 [...] -netdev user,id=net0 -device usb-net,netdev=net0
```
