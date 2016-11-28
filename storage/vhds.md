# Virtual Hard Disks
====================


https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-about-disks-vhds

Every vm needs an operating system disk, at a minimum.  Before the VM is run
(before or during vm creation) a source image must be copied into a local
storage account blob container.  This page blob must have the .vhd suffix.

These disks are in fixed-size VHD format, but the sparsity of the original
image is preserved - therefore, the actual size of the disk file to be copied
may be significantly less than the formatted capacity of the VHD.


```bash
$ azure storage container create -c 'DefaultEndpointsProtocol=https;AccountName=intro20161122strgprm;AccountKey=7sKhyLqDk8J5pSsloNrfU4MVohK7uu3qFwKJBX3ZT37UsI8WsH/FnRE7JmPskqXb2yBkHdalXJV5EqBKx17o3Q==' --container vhds 
info:    Executing command storage container create
+ Creating storage container vhds                                              
+ Getting storage container information                                        
data:    {
data:        name: 'vhds',
data:        metadata: {},
data:        etag: '"0x8D417379EAB3A69"',
data:        lastModified: 'Mon, 28 Nov 2016 02:38:21 GMT',
data:        lease: { status: 'unlocked', state: 'available' },
data:        requestId: 'd7c02823-001c-000d-3220-49dfa4000000',
data:        publicAccessLevel: 'Off'
data:    }
info:    storage container create command OK
```
