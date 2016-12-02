Gateways
========
A virtual network always has an implicit default gateway that connects
it to the public Internet and provides NAT ([VIPs](ips_nics.md)).  Similarly,
subnets within a single vnet know how to route to each other as well.  No
additional resources need be provisioned or configured to use these features.

Vnets within the same region can be connected using a [VPN Gateway](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways) or
(in some cases) [vnet peering] (https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview).
However, to connect a vnet to an on-premises network, or to a vnet in a different region, you must go through a VPN Gateway.

Azure supports these scenarios with system-defined routes.  Some scenarios
require [user-defined routes](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-udr-overview/).

When a gateway is needed, they must be provisioned into a specially-named
subnet.  This subnet must be called "GatewaySubnet".  Connections to other
VPN Gateways or other IPSEC endpoints are then made.  Note that these 
connections are one-way, so typically two connections are established between
each pair of endpoints. 
