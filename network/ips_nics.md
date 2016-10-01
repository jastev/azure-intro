IP Addresses & NICs
===================

## [Private vs. Public IPs](https://azure.microsoft.com/en-us/documentation/articles/virtual-network-ip-addresses-overview-arm/)

Because Azure virtual networks are logicially isolated, their apparent
address space is not directly publicly routable, even if it is not one of the
RFC1918 spaces (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).  For this reason
addresses on these networks are refererred to as _private_ IP addresses.  When
a private IP address is required, almost any address that is within the
network/subnet space may be be specified.  (Azure reserves the first and last,
plus three more.)

It is possible, of course, to obtain a publicly routable IP address.  These
addresses are not part of a virtual network, even if the public IP address
were to fall within an address space assigned to a vnet.  Public IPs are
allocated from a pool maintained within Azure, and must be requested - you
cannot specify a particular address.

Unlike private IPs, public IPs are represented as resources that you must
allocate and manage.

```bash
azure network public-ip create -g <resource-group> -n <name> -l <region>
```

```bash
azure network public-ip list -g <resource-group>
```

```bash
azure network public-ip show -g <resource-group> -n <name>
```

## Dynamic vs. Static IPs

By default, both private and public IP addresses are allocated dynamically
and can change periodically (they are allocated when a resource is started,
and released when a resource is stopped or deleted).  To specify that an
address is static, use the -a switch.  For private IP addresses, the 
argument to -a is the address itself.  For public IP addresses, the argument
to -a is the allocation method: "Dynamic" (the default) or "Static".

For example,

```bash
azure network public-ip create -g <resource-group> -n <name> -l <region> -a Static
```

## NICs & IP Address Binding

IP addresses can be assigned to some Azure resources directly (e.g. load
balancers).  Virtual machines, however, require that the address be assigned
to a NIC, which is then assigned to the VM. *This takes place at the time of
NIC creation and cannot be changed via the xplat CLI.*  While the CLI does
incude an 'azure network nic set' command, it does not support changing
the associated IP address.

```bash
azure network nic create -g <resource-group> -n <name> -l <region> -m <subnet-vnet-name> -a <private-ip>
azure network nic create -g <resource-group> -n <name> -l <region> -p <public-ip-name>
```

