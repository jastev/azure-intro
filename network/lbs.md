Load Balancers
==============

An Azure [load balancer](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview#load-balancer-features)
 maps incoming network connections from its front end to a back end pool of 
VMs.  Typically the traffic is mapped so as to distribute the load
equally, enabling scale out.  The load balancer monitors the health of
its back-end pool, and will manage the pool accordingly, ceasing to
distribute new connections to VMs that no longer respond suitably to health
probes.

```bash
# azure network lb create <resource-group> <lb-name> <location>

$ azure network lb create -g intro-rg -n intro-lb -l westus
info:    Executing command network lb create
+ Looking up the load balancer "intro-lb"                                      
+ Creating load balancer "intro-lb"                                            
data:    Id                              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb
data:    Name                            : intro-lb
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westus
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Load balancers can either be internet-facing, in which case we need a PIP
for its front end, or internal, in which case they are attached to a vnet
and subnet, and acquire a DIP normally.

Here we'll create a new PIP and use it to create the load balancer's front
end.

```bash
$ azure network public-ip create -g intro-rg -n intro-pip-lb -l westus
info:    Executing command network public-ip create
warn:    Using default --idle-timeout 4
warn:    Using default --allocation-method Dynamic
warn:    Using default --ip-version IPv4
+ Looking up the public ip "intro-pip-lb"                                      
+ Creating public ip address "intro-pip-lb"                                    
data:    Id                              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip-lb
data:    Name                            : intro-pip-lb
data:    Type                            : Microsoft.Network/publicIPAddresses
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Allocation method               : Dynamic
data:    IP version                      : IPv4
data:    Idle timeout in minutes         : 4
info:    network public-ip create command OK

$ azure network lb frontend-ip create -g intro-rg -l intro-lb -n intro-lb-front -i intro-pip-lb
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "intro-lb"                                      
+ Looking up the public ip "intro-pip-lb"                                      
+ Updating load balancer "intro-lb"                                            
data:    Name                            : intro-lb-front
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip-lb
info:    network lb frontend-ip create command OK
```

Now, we create the backend pool...

```bash
$ azure network lb address-pool create -g intro-rg -l intro-lb -n intro-lb-back
info:    Executing command network lb address-pool create
+ Looking up the load balancer "intro-lb"                                      
+ Updating load balancer "intro-lb"                                            
data:    Name                            : intro-lb-back
data:    Provisioning state              : Succeeded
info:    network lb address-pool create command OK
```

And add the two NICs we created earlier to it.

```bash
$ azure network nic ip-config set -g intro-rg -c intro-nic-be1 -n default-ip-config -d /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/loadbalancers/intro-lb/backendAddressPools/intro-lb-back
info:    Executing command network nic ip-config set
+ Looking up the network interface "intro-nic-be1"                             
+ Updating network interface "intro-nic-be1"                                   
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkInterfaces/intro-nic-be1
data:    Name                            : intro-nic-be1
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    MAC address                     : 00-0D-3A-35-0F-E1
data:    Internal domain name suffix     : heksuhpgkrnefhty1fwul5dpla.dx.internal.cloudapp.net
data:    Enable IP forwarding            : false
data:    Virtual machine                 : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Compute/virtualMachines/intro-vm-be1
data:    IP configurations:
data:      Name                          : default-ip-config
data:      Primary                       : true
data:      Provisioning state            : Succeeded
data:      Private IP address            : 10.1.0.4
data:      Private IP version            : IPv4
data:      Private IP allocation method  : Dynamic
data:      Subnet                        : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-1
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb/backendAddressPools/intro-lb-back
data:     
info:    network nic ip-config set command OK
MacBook-Pro:storage jastev$ azure network nic ip-config set -g intro-rg -c intro-nic-be2 -n default-ip-config -d /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/loadbalancers/intro-lb/backendAddressPools/intro-lb-back
info:    Executing command network nic ip-config set
+ Looking up the network interface "intro-nic-be2"                             
+ Updating network interface "intro-nic-be2"                                   
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkInterfaces/intro-nic-be2
data:    Name                            : intro-nic-be2
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    MAC address                     : 00-0D-3A-33-B4-77
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
data:      Load balancer backend address pools:
data:        Id                          : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb/backendAddressPools/intro-lb-back
data:     
info:    network nic ip-config set command OK
```

Finally, let's review the load balancer as we've set it up so far:

```bash
$ azure network lb show -g intro-rg -n intro-lb
info:    Executing command network lb show
+ Looking up the load balancer "intro-lb"                                      
data:    Id                              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb
data:    Name                            : intro-lb
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    
data:    Frontend IP configurations:
data:    Name            Provisioning state  Private IP allocation  Private IP   Subnet  Public IP   
data:    --------------  ------------------  ---------------------  -----------  ------  ------------
data:    intro-lb-front  Succeeded           Dynamic                                     intro-pip-lb
data:    
data:    Backend Address Pools:
data:    Name           Provisioning state
data:    -------------  ------------------
data:    intro-lb-back  Succeeded         
info:    network lb show command OK
```
