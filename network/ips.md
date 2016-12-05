IP Addresses
============

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

When a DIP initiates a connection to an external address, it
undergoes source network address translation (SNAT) within Azure.  If it
is not behind a [load balancer](lbs.md), it is mapped
to a temporary public IP address - a virtual IP address, or _VIP_, 
borrowed from its host.  A DIP that is behind an Internet-facing load 
balancer is SNATted to the PIP of the load balancer.

Another option is to request a more lasting assignment of a public IP address
from the Azure pool.  These instance-level public IPs (_ILPIP_ or just _PIP_),
unlike DIPs or VIPs, are represented as resources that you must
allocate and manage.

## Dynamic vs. Static IPs

By default, both private and public IP addresses are allocated dynamically
and can change periodically (they are allocated when a resource is started,
and released when a resource is stopped or deleted).  To specify that an
address is static, use the -a switch.  For private IP addresses, the
argument to -a is the address itself.  For public IP addresses, the argument
to -a is the allocation method: "Dynamic" (the default) or "Static".

```bash
# azure network public-ip create -a Static|Dynamic <resource-group-name> <public-ip-name> <region-name>
# azure network public-ip create -g <resource-group> -n <name> -l <region> -a Static|Dynamic

$ azure network public-ip create -g intro-rg -n intro-pip -l westus -a Static
info:    Executing command network public-ip create
warn:    Using default --idle-timeout 4
warn:    Using default --ip-version IPv4
+ Looking up the public ip "intro-pip"                                         
+ Creating public ip address "intro-pip"                                       
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip
data:    Name                            : intro-pip
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Allocation method               : Static
data:    IP version                      : IPv4
data:    Idle timeout in minutes         : 4
data:    IP Address                      : 13.91.100.127
info:    network public-ip create command OK
```

We'll need another PIP later for our load balancer, so we'll create that now
as well.

```bash
$ azure network public-ip create -g intro-rg -n intro-pip-lb -l westus -a Static
info:    Executing command network public-ip create
warn:    Using default --idle-timeout 4
warn:    Using default --ip-version IPv4
+ Looking up the public ip "intro-pip-lb"                                      
+ Creating public ip address "intro-pip-lb"                                    
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip-lb
data:    Name                            : intro-pip-lb
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Allocation method               : Static
data:    IP version                      : IPv4
data:    Idle timeout in minutes         : 4
data:    IP Address                      : 104.45.233.180
info:    network public-ip create command OK
```

```bash
# azure network public-ip list -g <resource-group-name>

$ azure network public-ip list -g intro-rg
info:    Executing command network public-ip list
+ Getting the public ip addresses                                              
data:    Name          Location  Resource group  Provisioning state  Allocation method  IP version  IP Address      Idle timeout, minutes  FQDN
data:    ------------  --------  --------------  ------------------  -----------------  ----------  --------------  ---------------------  ----
data:    intro-pip     westus    intro-rg        Succeeded           Static             IPv4        13.91.100.127   4                          
data:    intro-pip-lb  westus    intro-rg        Succeeded           Static             IPv4        104.45.233.180  4                          
info:    network public-ip list command OK
```

```bash
# azure network public-ip show -g <resource-group-name> -n <public-ip-name>

$ azure network public-ip show -g intro-rg -n intro-pip
info:    Executing command network public-ip show
+ Looking up the public ip "intro-pip"                                         
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip
data:    Name                            : intro-pip
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Allocation method               : Static
data:    IP version                      : IPv4
data:    Idle timeout in minutes         : 4
data:    IP Address                      : 13.91.100.127
data:    IP configuration id             : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkInterfaces/intro-nic/ipConfigurations/default-ip-config
info:    network public-ip show command OK
```
