Network Security Groups
=======================
Security in a virtual network is managed at the subnet and NIC level through
[Network Security Groups](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-nsg/), or nsgs.  An nsg works like a firewall: it comprises
a collection of ACL rules, each of which matches traffic against a pattern and
allows or denies it.  Inbound traffic passes through an nsg bound to the subnet
first, then through the nsg bound to the receiving NIC.  The reverse is true
for outbound traffic.

While a single nsg can be bound to any number of subnets and/or nics, 
each subnet or nic can be bound to only a single nsg.  This ability to reuse 
nsgs is important in large deployments, because the default quota for them is
100 per subscription per region.

```bash
# azure network nsg create [options] <resource-group> <name> <location>
# azure network nsg create -g <resource-group> -n <name> -l <location>

$ azure network nsg create -g intro-rg -n intro-nsg -l westus
info:    Executing command network nsg create
+ Looking up the network security group "intro-nsg"                            
+ Creating a network security group "intro-nsg"                                
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkSecurityGroups/intro-nsg
data:    Name                            : intro-nsg
data:    Type                            : Microsoft.Network/networkSecurityGroups
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Security rules:
data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
info:    network nsg create command OK
```

The default rules shown above allow inbound traffic from within the vnet
or from an attached [load balancer](lbs.md), but deny from all other sources.
Rules are checked in order of priority, and terminate with the first
matching rule.

We can extend the rules, for example allowing ssh (tcp port 22) like this:
```bash
# azure nsg rule create -g <resource-group> -a <nsg-name> -n <rule-name> -c Allow/Deny -r Inbound/Outbound -p Tcp/Udp/* -u <port> -y <priority>

$ azure network nsg rule create -g intro-rg -a intro-nsg -n allow-ssh-in -c Allow -r Inbound -p Tcp -u 22 -y 1000
info:    Executing command network nsg rule create
warn:    Using default --source-port-range *
warn:    Using default --source-address-prefix *
warn:    Using default --destination-address-prefix *
+ Looking up the network security group "intro-nsg"                            
+ Looking up the network security rule "allow-ssh-in"                          
+ Creating a network security rule "allow-ssh-in"                              
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkSecurityGroups/intro-nsg/securityRules/allow-ssh-in
data:    Name                            : allow-ssh-in
data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
data:    Provisioning state              : Succeeded
data:    Source IP                       : *
data:    Source Port                     : *
data:    Destination IP                  : *
data:    Destination Port                : 22
data:    Protocol                        : Tcp
data:    Direction                       : Inbound
data:    Access                          : Allow
data:    Priority                        : 1000
info:    network nsg rule create command OK
```

Showing the NSG or listing the rules will verify that the rule has been added:

```bash
$ azure network nsg show -g intro-rg -n intro-nsg
info:    Executing command network nsg show
+ Looking up the network security group "intro-nsg"                            
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkSecurityGroups/intro-nsg
data:    Name                            : intro-nsg
data:    Type                            : Microsoft.Network/networkSecurityGroups
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Security rules:
data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
data:    allow-ssh-in                   *                  *            *               22                Tcp       Inbound    Allow   1000    
data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
info:    network nsg show command OK
```

```bash
$ azure network nsg rule list -g intro-rg -a intro-nsg
info:    Executing command network nsg rule list
+ Looking up the network security group "intro-nsg"                            
data:    Security rules:
data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
data:    allow-ssh-in                   *                  *            *               22                Tcp       Inbound    Allow   1000    
data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
info:    network nsg rule list command OK
```

Once the NSG has been created, it can be applied to a subnet or NIC.

```bash
$ azure network nic set -g intro-rg -n intro-nic -o intro-nsg
info:    Executing command network nic set
+ Looking up the network interface "intro-nic"                                 
+ Looking up the network security group "intro-nsg"                            
+ Updating network interface "intro-nic"                                       
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkInterfaces/intro-nic
data:    Name                            : intro-nic
data:    Type                            : Microsoft.Network/networkInterfaces
data:    Location                        : westus
data:    Provisioning state              : Succeeded
data:    Internal domain name suffix     : heksuhpgkrnefhty1fwul5dpla.dx.internal.cloudapp.net
data:    Enable IP forwarding            : false
data:    Network security group          : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/networkSecurityGroups/intro-nsg
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
info:    network nic set command OK
```

