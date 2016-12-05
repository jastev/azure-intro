Virtual Networks & Subnets
==========================
An Azure [virtual network](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-overview/)
(or "vnet") is a resource that provides a logical IP communication space for
other resources.  It comprises one or more IPv4 address spaces.

By default, vnets are independent of each other and the public Internet - you
can have the same or overlapping address space in two different vnets, or in 
a vnet and the public Internet.  Even if the address space in a vnet is 
nominally in routable IP space, by default it will not be reachable from the 
public Internet.

If vnets are interconnected, or to on-premises networks, however,
the address spaces cannot intersect each other, because of routing constraints.

Note that like any other resource, vnets must be created in a region,
and cannot span regions.

```bash
# azure network vnet create -g <resource-group> -n <name> -l <location> -a <address-space-csv>

$ azure network vnet create -g intro-rg -n intro-vnet -l westus -a 10.0.0.0/8,192.168.0.0/16
info:    Executing command network vnet create
+ Looking up the virtual network "intro-vnet"                                  
+ Creating virtual network "intro-vnet"                                        
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet
data:    Name                            : intro-vnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Address prefixes:
data:      10.0.0.0/8
data:      192.168.0.0/16
info:    network vnet create command OK
```

```bash
# azure network vnet list -g <resource-group>

$ azure network vnet list -g intro-rg
info:    Executing command network vnet list
+ Looking up virtual networks                                                  
data:    Name        Location  Resource group  Provisioning state  Address prefixes           DNS servers  Subnets number
data:    ----------  --------  --------------  ------------------  -------------------------  -----------  --------------
data:    intro-vnet  westus    intro-rg        Succeeded           10.0.0.0/8,192.168.0.0/16                             
info:    network vnet list command OK
```

```bash
# azure network vnet show -g <resource-group> -n <vnet-name>

$ azure network vnet show -g intro-rg -n intro-vnet
info:    Executing command network vnet show
+ Looking up the virtual network "intro-vnet"                                  
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet
data:    Name                            : intro-vnet
data:    Type                            : Microsoft.Network/virtualNetworks
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Address prefixes:
data:      10.0.0.0/8
data:      192.168.0.0/16
info:    network vnet show command OK
```

## Subnets

Virtual networks are themselved composed of subnets, which each have a single
address space expressable in CIDR notation that must be within that defined
by its parent vnet.  Network resources always exist within a subnet, rather
than the parent vnet itself.

```bash
# azure network vnet subnet create <resource-group-name> <vnet-name> <subnet-name>
# azure network vnet subnet create -g <resource-group-name> -e <vnet-name> -n <subnet-name> -a <address-space>

$ azure network vnet subnet create -g intro-rg -e intro-vnet -n intro-subnet-10-0 -a 10.0.0.0/16
info:    Executing command network vnet subnet create
+ Looking up the virtual network "intro-vnet"                                  
+ Looking up the subnet "intro-subnet-10-0"                                    
+ Creating subnet "intro-subnet-10-0"                                          
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-0
data:    Name                            : intro-subnet-10-0
data:    Provisioning state              : Succeeded
data:    Address prefix                  : 10.0.0.0/16
info:    network vnet subnet create command OK
```

```bash
$ azure network vnet subnet create -g intro-rg -e intro-vnet -n intro-subnet-10-1 -a 10.1.0.0/16
info:    Executing command network vnet subnet create
+ Looking up the virtual network "intro-vnet"                                  
+ Looking up the subnet "intro-subnet-10-1"                                    
+ Creating subnet "intro-subnet-10-1"                                          
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-1
data:    Name                            : intro-subnet-10-1
data:    Provisioning state              : Succeeded
data:    Address prefix                  : 10.1.0.0/16
info:    network vnet subnet create command OK
```

```bash
# azure network vnet subnet list <resource-group-name> <vnet-name>
# azure network vnet subnet list -g <resource-group-name> -e <vnet-name>

$ azure network vnet subnet list -g intro-rg -e intro-vnet
info:    Executing command network vnet subnet list
+ Looking up the virtual network "intro-vnet"                                  
+ Getting virtual network subnets                                              
data:    Name               Provisioning state  Address prefix
data:    -----------------  ------------------  --------------
data:    intro-subnet-10-0  Succeeded           10.0.0.0/16   
data:    intro-subnet-10-1  Succeeded           10.1.0.0/16   
info:    network vnet subnet list command OK
```

```bash
# azure network vnet subnet show <resource-group-name> <vnet-name> <subnet-name>
# azure network vnet subnet show -g <resource-group-name> -e <vnet-name> -n <subnet-name>

$ azure network vnet subnet show -g intro-rg -e intro-vnet -n intro-subnet-10-0
info:    Executing command network vnet subnet show
+ Looking up the virtual network "intro-vnet"                                  
+ Looking up the subnet "intro-subnet-10-0"                                    
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-0
data:    Name                            : intro-subnet-10-0
data:    Provisioning state              : Succeeded
data:    Address prefix                  : 10.0.0.0/16
info:    network vnet subnet show command OK
```

There is a special subnet that does not exist by default, called 
"GatewaySubnet", that holds a vnet's [VPN Gateways](gateways.md).
