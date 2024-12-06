# vhost-user back ends

- vhost-user back ends are way to service the request of VirtIO devices outside of QEMU itself.

- To do this there are a number of things required.

## vhost-user device

- These are simple stub devices that ensure the VirtIO device is visible to the guest.

- The code is mostly boilerplate although each device has a chardev option which specifies the ID of the `--chardev` device that connects via a socket to the vhost-user daemon.

<br>

- Each device will have an virtio-mmio and virtio-pci variant.

- See your platform details for what sort of virtio bus to use.

### vhost-user devices

|Device|Type|Notes|
|-|-|-|
|vhost-user-blk|Block storage|See contrib/vhost-user-blk|
|vhost-user-fs|File based storage driver|See https://gitlab.com/virtio-fs/virtiofsd|
|vhost-user-gpio|Proxy gpio pins to host|See https://github.com/rust-vmm/vhost-device|
|vhost-user-gpu|GPU driver|See contrib/vhost-user-gpu|
|vhost-user-i2c|Proxy i2c devices to host|See https://github.com/rust-vmm/vhost-device|
|vhost-user-input|Generic input driver|QEMU vhost-user-input - Input emulation|
|vhost-user-rng|Entropy driver|QEMU vhost-user-rng - RNG emulation|
|vhost-user-scmi|System Control and Management Interface|See https://github.com/rust-vmm/vhost-device|
|vhost-user-snd|Audio device|See https://github.com/rust-vmm/vhost-device/staging|
|vhost-user-scsi|SCSI based storage|See contrib/vhost-user-scsi|
|vhost-user-vsock|Socket based communication|See https://github.com/rust-vmm/vhost-device|

- The referenced daemons are not exhaustive, any conforming backend implementing the device and using the vhost-user protocol should work.

#### vhost-user-device

- The vhost-user-device is a generic development device intended for expert use while developing new backends.

- The user needs to specify all the required parameters including:

    - Device `virtio-id`

    - The `num_vqs` it needs and their `vq_size`

    - The `config_size` if needed

> ##### Note
>
> - To prevent user confusion you cannot currently instantiate vhost-user-device without first patching out:
>
>   ```sh
>   /* Reason: stop inexperienced users confusing themselves */
>   dc->user_creatable = false;
>   ```
>
>   - in `vhost-user-device.c` and `vhost-user-device-pci.c` file and rebuilding.

## vhost-user daemon

- This is a separate process that is connected to by QEMU via a socket following the Vhost-user Protocol.

- There are a number of daemons that can be built when enabled by the project although any daemon that meets the specification for a given device can be used.

## Shared memory object

- In order for the daemon to access the VirtIO queues to process the requests it needs access to the guest’s address space.

- This is achieved via the `memory-backend-file`, `memory-backend-memfd`, or `memory-backend-shm` objects.

- A reference to a file-descriptor which can access this object will be passed via the socket as part of the protocol negotiation.

- Currently the shared memory object needs to match the size of the main system memory as defined by the `-m` argument.

## Example

- First start your daemon.

    ```sh
    $ virtio-foo --socket-path=/var/run/foo.sock $OTHER_ARGS
    ```

- Then you start your QEMU instance specifying the device, chardev and memory objects.

    ```sh
    $ qemu-system-x86_64 \
        -m 4096 \
        -chardev socket,id=ba1,path=/var/run/foo.sock \
        -device vhost-user-foo,chardev=ba1,$OTHER_ARGS \
        -object memory-backend-memfd,id=mem,size=4G,share=on \
        -numa node,memdev=mem \
          ...
    ```
