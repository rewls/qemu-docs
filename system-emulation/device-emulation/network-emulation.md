# Network emulation

- QEMU can simulate several network cards (e.g. PCI or ISA cards on the PC target) and can connect them to a network backend on the host or an emulated hub.

- The various host network backends can either be used to connect the NIC of the guest to a real network (e.g. by using a TAP devices or the non-privileged user mode network stack), or to other guest instances running in another QEMU process (e.g. by using the socket host network backend).

## Using the user mode network stack

- By using the option `-netdev user` (default configuration if no `-netdev` option is specified), QEMU uses a completely user mode network stack (you don’t need root privilege to use the virtual network).

- The virtual network configuration is the following:

    ```
    guest (10.0.2.15)  <------>  Firewall/DHCP server <-----> Internet
                          |          (10.0.2.2)
                          |
                          ---->  DNS server (10.0.2.3)
                          |
                          ---->  SMB server (10.0.2.4)
    ```

- The QEMU VM behaves as if it was behind a firewall which blocks all incoming connections.

- You can use a DHCP client to automatically configure the network in the QEMU VM.

- The DHCP server assign addresses to the hosts starting from 10.0.2.15.

<br>

- In order to check that the user mode network is working, you can ping the address 10.0.2.2 and verify that you got an address in the range 10.0.2.x from the QEMU virtual DHCP server.

<br>

- Note that ICMP traffic in general does not work with user mode networking.

- `ping`, aka. ICMP echo, to the local router (10.0.2.2) shall work, however.

- If you’re using QEMU on Linux >= 3.0, it can use unprivileged ICMP ping sockets to allow `ping` to the Internet.

- The host admin has to set the `ping_group_range` in order to grant access to those sockets.

- To allow ping for GID 100 (usually users group):

    ```sh
    $ echo 100 100 > /proc/sys/net/ipv4/ping_group_range
    ```
