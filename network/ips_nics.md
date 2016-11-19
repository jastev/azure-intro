IP Addresses & NICs
===================

## [Private vs. Public IPs](https://azure.microsoft.com/en-us/documentation/articles/virtual-network-ip-addresses-overview-arm/)

Because Azure virtual networks are logicially isolated, their apparent
address space is not directly publicly routable, even if it is not one of the
RFC1918 spaces (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).  For this reason
addresses on these networks are refererred to as _private_ IP addresses.  When
a private IP address is required, almost any address that is within the
network/subnet space may be be specified.  (Azure reserves the first and last,
plus three more.  Virtual networks do not support multicast or broadcast
addressing.)  These are assigned dynamically via DHCP, so these are called
Dynamic IPs or _DIPs_.

If the DIPs are not externally routable, how can they [commnicate with the
public Internet](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-outbound-connections)?  Via a _public_ IP address, of course.  These
addresses are not part of a virtual network, even if the public IP address
were to fall within an address space assigned to a vnet.  Public IPs are
allocated from a pool maintained within Azure - you cannot specify a 
particular address.

When a DIP initiates a connection to an extnernal address, it
undergoes source network address translation (SNAT) within Azure.  If it
is not behind a [load balancer](lbs.md), it is mapped
to a temporary public IP address - a virtual IP address, or _VIP_. 

Another option is to request a more lasting assignment of a public IP address
from the Azure pool.  These instance-level public IPs (_ILPIP_ or just _PIP_),
unlike DIPs or VIPs, are represented as resources that you must
allocate and manage.

```bash
# azure network public-ip create <resource-group-name> <public-ip-name> <region-name>
# azure network public-ip create -g <resource-group> -n <name> -l <region>

$ azure network public-ip create -g intro-rg -n intro-pip -l westus
info:    Executing command network public-ip create
warn:    Using default --idle-timeout 4
warn:    Using default --allocation-method Dynamic
warn:    Using default --ip-version IPv4
+ Looking up the public ip "intro-pip"                                         
+ Creating public ip address "intro-pip"                                       
data:    Id                              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip
data:    Name                            : intro-pip
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    IP version                      : IPv4
data:    Idle timeout in minutes         : 4
info:    network public-ip create command OK
```

```bash
azure network public-ip list -g <resource-group-name>
```

```bash
azure network public-ip show -g <resource-group-name> -n <public-ip-name>
```

A DIP that is behind an Internet-facing load balancer is SNATted to the PIP
of the load balancer.

## Dynamic vs. Static IPs

By default, both private and public IP addresses (DIPs and VIPs) are allocated dynamically
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
to a virtual [Network Interface Card (NIC)](https://azure.microsoft.com/en-us/documentation/articles/virtual-network-network-interface-overview/), which is then assigned to the VM. *This takes place at the time of
NIC creation and cannot be changed later via the xplat CLI.*  While the CLI does
incude an 'azure network nic set' command, it does not support changing
the associated IP address.

```bash
# azure network nic create -g <resource-group> -n <name> -l <region> -m <vnet-name> -k <subnet-name> -a <private-ip>
# azure network nic create -g <resource-group> -n <name> -l <region> -p <public-ip-name>

$ azure network nic create -g intro-rg -n intro-nic -l westus -m intro-vnet -k intro-subnet-10-0 -p intro-pip
info:    Executing command network nic create
+ Looking up the network interface "intro-nic"                                 
+ Looking up the subnet "intro-subnet-10-0"                                    
+ Looking up the public ip "intro-pip"                                         
+ Creating network interface "intro-nic"                                       
data:    Id                              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Network/networkInterfaces/intro-nic
data:    Name                            : intro-nic
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Internal domain name suffix     : heksuhpgkrnefhty1fwul5dpla.dx.internal.cloudapp.net
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : default-ip-config
data:      Primary                       : true
data:      Provisioning state            : Succeeded
data:      Private IP address            : 10.0.0.4
data:      Private IP version            : IPv4
data:      Private IP allocation method  : Dynamic
data:      Public IP address             : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip
data:      Subnet                        : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-0
data:     
info:    network nic create command OK
```

Note that while at present each NIC can have only one IP address assigned to
it, some types of VMs can have multiple NICs.  However, in order to enable this feature, the VM must be assigned at least two NICs at creation.  A VM that is
created with a single NIC will not support adding a second one later, even if
its type allows this in general.

You can set various properties of a NIC, including its [Network Security
Group](nsgs.md), its [DNS](dns.md) name, and IP forwarding, with

```bash
# azure network nic set
``` 
