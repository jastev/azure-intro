NICs & IP Address Binding
=========================

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
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkInterfaces/intro-nic
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
data:      Public IP address             : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip
data:      Subnet                        : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-0
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

Other properties of the NIC are stored within an IP configuration.  A NIC can
have more than one IP configuration.  These are not top-level resources; they
exist only within their parent NIC.  The IP configuration includes not only
the information shown above, but also what [load balancer](lbs.md) pool the
NIC is part of.  An IP configuration is created for you if you don't create
one yourself.  You can set properties on the IP configuration with

```bash
# azure network nic ip-config set -g <resource-group> -c <nic> -n <ip-config-name>
```

Let's create two more NICs, with DIPs rather than PIPs.  We'll use these
later.  Note that these are in the other subnet.

```bash
$ azure network nic create -g intro-rg -n intro-nic-be1 -l westus -m intro-vnet -k intro-subnet-10-1
info:    Executing command network nic create
+ Looking up the network interface "intro-nic-be1"                             
+ Looking up the subnet "intro-subnet-10-1"                                    
+ Creating network interface "intro-nic-be1"                                   
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkInterfaces/intro-nic-be1
data:    Name                            : intro-nic-be1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Internal domain name suffix     : heksuhpgkrnefhty1fwul5dpla.dx.internal.cloudapp.net
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : default-ip-config
data:      Primary                       : true
data:      Provisioning state            : Succeeded
data:      Private IP address            : 10.1.0.4
data:      Private IP version            : IPv4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-1
data:     
info:    network nic create command OK
```

```bash
$ azure network nic create -g intro-rg -n intro-nic-be2 -l westus -m intro-vnet -k intro-subnet-10-1
info:    Executing command network nic create
+ Looking up the network interface "intro-nic-be2"                             
+ Looking up the subnet "intro-subnet-10-1"                                    
+ Creating network interface "intro-nic-be2"                                   
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkInterfaces/intro-nic-be2
data:    Name                            : intro-nic-be2
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Internal domain name suffix     : heksuhpgkrnefhty1fwul5dpla.dx.internal.cloudapp.net
data:    Enable IP forwarding            : false
data:    IP configurations:
data:      Name                          : default-ip-config
data:      Primary                       : true
data:      Provisioning state            : Succeeded
data:      Private IP address            : 10.1.0.5
data:      Private IP version            : IPv4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-1
data:     
info:    network nic create command OK
```

```bash
$ azure network nic list -g intro-rg
info:    Executing command network nic list
+ Getting the network interfaces                                               
data:    Name           Location  Resource group  Provisioning state  MAC Address        IP forwarding  Internal DNS name  Internal FQDN  Internal domain name suffix                        
data:    -------------  --------  --------------  ------------------  -----------------  -------------  -----------------  -------------  ---------------------------------------------------
data:    intro-nic      westus    intro-rg        Succeeded           00-0D-3A-32-37-1C  false                                            oo1wlrzw0jmelof2yc4p53kbnf.dx.internal.cloudapp.net
data:    intro-nic-be1  westus    intro-rg        Succeeded           00-0D-3A-34-FE-26  false                                            oo1wlrzw0jmelof2yc4p53kbnf.dx.internal.cloudapp.net
data:    intro-nic-be2  westus    intro-rg        Succeeded           00-0D-3A-33-70-5B  false                                            oo1wlrzw0jmelof2yc4p53kbnf.dx.internal.cloudapp.net
info:    network nic list command OK
```
