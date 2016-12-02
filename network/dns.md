Azure DNS Zones
===============
Azure DNS lets you manage [zones and records](https://docs.microsoft.com/en-us/azure/dns/dns-zones-records).

First, we need to [create a zone](https://docs.microsoft.com/en-us/azure/dns/dns-getstarted-create-dnszone-cli).  Note that because DNS is a global service, 
unlike most other resources, you do not need to specify a location for the zone.

```bash
$ azure network dns zone create -g intro-rg -n intro.on-azure.info
info:    Executing command network dns zone create
+ Creating dns zone "intro.on-azure.info"                                      
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/dnszones/intro.on-azure.info
data:    Name                            : intro.on-azure.info
data:    Type                            : Microsoft.Network/dnszones
data:    Location                        : global
data:    Number of record sets           : 2
data:    Max number of record sets       : 5000
data:    Name servers:
data:        ns1-01.azure-dns.com.
data:        ns2-01.azure-dns.net.
data:        ns3-01.azure-dns.org.
data:        ns4-01.azure-dns.info.
data:    Metadata                        : 
info:    network dns zone create command OK
```

Once the zone has been created, you can see that the NS and SOA record sets
are created by default:

```bash
# azure network dns record-set list -g <resource-group> -z <zone>

$ azure network dns record-set list -g intro-rg -z intro.on-azure.info
info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets                                               
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:     
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 1
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:     
info:    network dns record-set list command OK
```

To add DNS records to our zone, we must first [create a new record set](https://docs.microsoft.com/en-us/azure/dns/dns-getstarted-create-recordset-cli):

```bash
# azure network dns record-set create -g <resource-group> -z <zone> -n <dns-name> -y <record-type>

$ azure network dns record-set create -g intro-rg -z intro.on-azure.info -n www -y A
info:    Executing command network dns record-set create
warn:    using default TTL of 3600 seconds
+ Creating DNS record set "www" of type "A"                                      
data:    Id                              : /subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/dnszones/intro.on-azure.info/A/www
data:    Name                            : *
data:    Type                            : Microsoft.Network/dnszones/A
data:    TTL                             : 3600
data:    Metadata                        : 
info:    network dns record-set create command OK
```

We can then add a record to this set.  In this case, we'll use the PIP of 
the load balancer that we created earlier.

```bash
$ azure network dns record-set add-record -g intro-rg -z intro.on-azure.info -n www -y A -a 104.45.233.180
info:    Executing command network dns record-set add-record
+ Looking up the dns zone "intro.on-azure.info"                                
+ Looking up the DNS Record Set "www" of type "A"                              
+ Updating record set "www" of type "A"                                        
data:    Id                              : /subscriptions/1111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Network/dnszones/intro.on-azure.info/A/*
data:    Name                            : www
data:    Type                            : Microsoft.Network/dnszones/A
data:    TTL                             : 3600
data:    Metadata                        : 
data:    A records:
data:        IPv4 address                : 104.45.233.180
data:     
data:     
info:    network dns record-set add-record command OK
```
