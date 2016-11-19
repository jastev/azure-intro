Gateways
========
A virtual network always has an implicit default gateway that connects
it to the public Internet and provides NAT ([VIPs](ips_nics.md)).  Vnets
within the same region can be connected using [vnet peering]
(https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview).  However, to connect a vnet to an on-premises network, or to a
vnet in a different region, you must go through a [VPN Gateway](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).

Azure supports these scenarios with system-defined routes.  Some scenarios
require [user-defined routes](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-udr-overview/).

The design and management of complex network topologies are outside
the scope of an introduction such as this.
