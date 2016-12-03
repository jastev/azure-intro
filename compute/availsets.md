Availability Sets
=================

Outside of a VM itself, there are two kinds of events that might cause a VM
to go down: planned events, such as maintenance, and unplanned events, such
as a hardware failure.  In order to ensure that your services remain highly
available despite such events, Azure allows grouping VMs into [availability
sets](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-manage-availability).

An availabilty set comprises two different kinds of domains: 
update domains and fault domains.  Scheduled maintenance is rolled out
in waves, across update domains: VMs in two different update domains will
not be taken down and rebooted at the same time.  Similarly, fault domains
encapsulate single points of hardware failure for a VM's host: power, network,
etc.  VMs in two different fault domains will not both be affected by the
failure of hardware within a given rack.

When you create an availability set, you specify the number of update
domains (default 5) and fault domains (default 3) that it should span.  New
VMs are added round-robin to these domains.

```bash
# azure availset create -g <resource-group> -n <availability-set-name> -l <location> -a <update-domains> -b <fault-domains>

$ azure availset create -g intro-rg -n intro-availset -l westus
info:    Executing command availset create
+ Looking up the availability set "intro-availset"                             
+ Creating availability set "intro-availset"                                   
info:    availset create command OK
```

```bash
$ azure availset show -g intro-rg -n intro-availset
info:    Executing command availset show
+ Looking up the availability set "intro-availset"                             
data:    id "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/intro-rg/providers/Microsoft.Compute/availabilitySets/intro-availset"
data:    name "intro-availset"
data:    type "Microsoft.Compute/availabilitySets"
data:    location "westus"
data:    platformUpdateDomainCount 5
data:    platformFaultDomainCount 3
data:    virtualMachines 0 id "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/INTRO-RG/providers/Microsoft.Compute/virtualMachines/INTRO-VM-BE1"
info:    availset show command OK
```

Note that there is a difference between having a single VM in an availability
set, and not having that VM in one at all.  If the VM is not in an availability
set, then Azure will attempt to notify (e.g. via email) the owner before
maintenance.  If it is in an availability set, then Azure assumes that such
notification is not necessary.

Also: fault domains represent single points of failure in the racks where
the VM host hardware operates, including networking hardware.  VMs require
access to their VHDs as well, though, and these are not mapped to fault
domains.  Make sure VHDs for the VMs in an availablity set are on different
storage accounts for the highest availability.
