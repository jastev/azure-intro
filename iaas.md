The IaaS "Big Three": Network, Storage, and Compute
===================================================
Infrastructure as a Service deployments in Azure usually resemble their physical
counterparts: CPUs aren't very useful without disks to hold the opertating
system, application, and data, and are almost always attached to some kind of 
network.

Azure IaaS implements these general services as Resource Providers, each 
offering a variety of Resource Types at a more granular abstraction layer.
Instances of these resources are assembled together into whole solutions.

[Network](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-overview/)
