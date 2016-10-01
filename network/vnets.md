Virtual Networks & Subnets
==========================
An Azure [virtual network](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-overview/)
(or "vnet") is a resource that provides a logical IP communication space for
other resources.  It comprises one or more IPv4 address spaces.

VNets are independent of each other and the public Internet - you can have 
the same or overlapping address space in two different vnets, or in a vnet
and the public Internet.  Even if the address space in a vnet is nominally
in routable IP space, it will not be routable from the public Internet.  All
of this is due to the fact that these address spaces are isolated from
everything outside the vnet through NAT.

Note that like any other resource, vnets must be created in a region,
and cannot span regions.

```bash
# azure network vnet create -g <resource-group> -n <name> -l <location> -a <address-space-csv>

$ azure network vnet create -g intro-rg -n intro-vnet -l westus -a 10.0.0.0/8,192.168.0.0/16
info:    Executing command network vnet create
+ Looking up the virtual network "intro-vnet"                                  
+ Creating virtual network "intro-vnet"                                        
data:    Id                              : /subscriptions/<subscription-id>/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet
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
data:    Id                              : /subscriptions/<subscription-id>/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet
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

```bash
azure network vnet subnet create -g <resource-group> -e <vnet-name> -n <name> -a <address-space>
```

```bash
azure network vnet subnet list -g <resource-group>
```

```bash
azure network vnet subnet show -g <resource-group> -n <subnet-name>
```

