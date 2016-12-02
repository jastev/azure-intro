Virtual Machines
================







```bash
$ azure vm create -g intro-rg -n intro-vm -f intro-nic -l westus -y Linux -Q UbuntuLTS -u intro -M ~/.ssh/id_rsa.pub -z Standard_DS1 -o intro20161122strg  -d intro.vhd
info:    Executing command vm create
+ Looking up the VM "intro-vm"                                                 
info:    Verifying the public key SSH file: ~/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account intro20161122strg                             
+ Looking up the NIC "intro-nic"                                               
info:    Found an existing NIC "intro-nic"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-0" in the NIC "intro-nic"
info:    This NIC IP configuration has a public ip already configured "/subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourcegroups/intro-rg/providers/microsoft.network/publicipaddresses/intro-pip", any public ip parameters if provided, will be ignored.
info:    The storage URI 'https://intro20161122strg.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
+ Creating VM "intro-vm"                                                       
info:    vm create command OK
```

Now we'll create the two VMs that will be behind the load balancer.  Note that
we're setting the boot diagnostics to be stored in the original strorage
account.

```bash
$ azure vm create -g intro-rg -n intro-vm-be1 -f intro-nic-be1 -l westus -y Linux -Q UbuntuLTS -u intro -M ~/.ssh/id_rsa.pub -z Standard_DS1 -o intro20161122strgprm -d intro-be1.vhd -r intro-availset --boot-diagnostics-storage-uri https://intro20161122strg.blob.core.windows.net/
info:    Executing command vm create
+ Looking up the VM "intro-vm-be1"                                             
info:    Verifying the public key SSH file: /Users/jastev/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account intro20161122strgprm                          
+ Looking up the availability set "intro-availset"                             
info:    Found an Availability set "intro-availset"
+ Looking up the NIC "intro-nic-be1"                                           
info:    Found an existing NIC "intro-nic-be1"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-1" in the NIC "intro-nic-be1"
info:    This is an NIC without publicIP configured
+ Creating VM "intro-vm-be1"                                                   
info:    vm create command OK
```

```bash
$ azure vm create -g intro-rg -n intro-vm-be2 -f intro-nic-be2 -l westus -y Linux -Q UbuntuLTS -u intro -M ~/.ssh/id_rsa.pub -z Standard_DS1 -o intro20161122strgprm -d intro-be2.vhd -r intro-availset --boot-diagnostics-storage-uri https://intro20161122strg.blob.core.windows.net/
info:    Executing command vm create
+ Looking up the VM "intro-vm-be2"                                             
info:    Verifying the public key SSH file: /Users/jastev/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account intro20161122strgprm                          
+ Looking up the availability set "intro-availset"                             
info:    Found an Availability set "intro-availset"
+ Looking up the NIC "intro-nic-be2"                                           
info:    Found an existing NIC "intro-nic-be2"
info:    Found an IP configuration with virtual network subnet id "/subscriptions/25b347b0-e6dd-45c1-bb11-529e36438d8f/resourceGroups/intro-rg/providers/Microsoft.Network/virtualNetworks/intro-vnet/subnets/intro-subnet-10-1" in the NIC "intro-nic-be2"
info:    This is an NIC without publicIP configured
+ Creating VM "intro-vm-be2"                                                   
info:    vm create command OK
```
