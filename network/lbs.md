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
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb
data:    Name                            : intro-lb
data:    Type                            : Microsoft.Network/loadBalancers
data:    Location                        : westus
data:    Provisioning state              : Succeeded
info:    network lb create command OK
```

Load balancers can either be internet-facing, in which case we need a PIP
for its front end, or internal, in which case they are attached to a vnet
and subnet, and acquire a DIP normally.

Here we'll use the second PIP we created earlier to create the load 
balancer's front end.

```bash
$ azure network lb frontend-ip create -g intro-rg -l intro-lb -n intro-lb-front -i intro-pip-lb
info:    Executing command network lb frontend-ip create
+ Looking up the load balancer "intro-lb"                                      
+ Looking up the public ip "intro-pip-lb"                                      
+ Updating load balancer "intro-lb"                                            
data:    Name                            : intro-lb-front
data:    Provisioning state              : Succeeded
data:    Private IP allocation method    : Dynamic
data:    Public IP address id            : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/publicIPAddresses/intro-pip-lb
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

And add the two NICs we created previously to it.

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
```

```bash
$ azure network nic ip-config set -g intro-rg -c intro-nic-be2 -n default-ip-config -d /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/loadbalancers/intro-lb/backendAddressPools/intro-lb-back
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

Now, we tell the load balancer to distribute incoming  HTTP traffic on port 80
across the back end pool.

```bash
$ azure network lb rule create -g intro-rg -l intro-lb -n intro-lb-rule-http -p tcp -f 80 -b 80 -t intro-lb-front -o intro-lb-back
info:    Executing command network lb rule create
+ Looking up the load balancer "intro-lb"                                      
warn:    Using default --idle-timeout 4
warn:    Using default --enable-floating-ip false
warn:    Using default --load-distribution Default
+ Updating load balancer "intro-lb"                                            
data:    Name                            : intro-lb-rule-http
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Frontend port                   : 80
data:    Backend port                    : 80
data:    Enable floating IP              : false
data:    Load distribution               : Default
data:    Idle timeout in minutes         : 4
data:    Frontend IP configuration id    : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb/frontendIPConfigurations/intro-lb-front
data:    Backend address pool id         : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb/backendAddressPools/intro-lb-back
info:    network lb rule create command OK
```
 
And we add a health probe that will ensure that VMs not responding to HTTP
requests are removed from the pool:

```bash
$ azure network lb probe create -g intro-rg -l intro-lb -n intro-lb-probe-http -p tcp -o 80 -f /
info:    Executing command network lb probe create
warn:    --path will be ignored when probe protocol is TCP
+ Looking up the load balancer "intro-lb"                                      
+ Updating load balancer "intro-lb"                                            
data:    Name                            : intro-lb-probe-http
data:    Provisioning state              : Succeeded
data:    Protocol                        : Tcp
data:    Port                            : 80
data:    Interval in seconds             : 15
data:    Number of probes                : 2
info:    network lb probe create command OK
```

Finally, let's review the load balancer as we've set it up so far:

```bash
$ azure network lb show -g intro-rg -n intro-lb
info:    Executing command network lb show
+ Looking up the load balancer "intro-lb"                                      
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/loadBalancers/intro-lb
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
data:    Probes:
data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
data:    -------------------  ------------------  --------  ----  ----  --------  -----
data:    intro-lb-probe-http  Succeeded           Tcp       80          15        2    
data:    
data:    Backend Address Pools:
data:    Name           Provisioning state
data:    -------------  ------------------
data:    intro-lb-back  Succeeded         
data:    
data:    Load Balancing Rules:
data:    Name                Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
data:    ------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
data:    intro-lb-rule-http  Succeeded           Default            Tcp       80             80            false               4                      
info:    network lb show command OK
```
