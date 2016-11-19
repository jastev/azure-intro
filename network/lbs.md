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

And finally, let's review the load balancer as we've set it up so far:

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
