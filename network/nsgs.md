Network Security Groups
=======================
Security in a virtual network is managed at the subnet and NIC level through
[Network Security Groups](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-nsg/), or nsgs.  An nsg works like a firewall: it comprises
a collection of rules, each of which matches traffic against a pattern and
allows or denies it.  Inbound traffic passes through an nsg bound to the subnet
first, then through the nsg bound to the receiving NIC.  The reverse is true
for outbound traffic.

While a single nsg can be bound to any number of subnets and/or nics, 
each subnet or nic can be bound to only a single nsg.  This ability to reuse 
nsgs is important in large deployments, because the default quota for them is
100 per subscription per region.
