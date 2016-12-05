# Virtual Hard Disks
====================

[Virtual hard disks](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-about-disks-vhds)
 in Azure are in a fixed-size VHD format, but the sparsity 
of the original image is preserved - therefore, the actual size of the disk 
file (e.g. during a copy) may be significantly less than the formatted capacity
of the VHD.  The VHDX format is not supported.

Every VM needs an operating system disk, at a minimum.  Before the VM is run
(before or during vm creation) a source image for this disk must be copied
into a local storage account blob container.  This page blob must have the
.vhd suffix.

If using a standard Azure image, it's most convenient to do this during VM
creation, when Azure will take care of this copy for you.  It's also possible
to use a custom image, though, in which case the source image will need to be 
prepared in advance and copied into the storage account. 

The default container for VHDs is, not surprisingly, called "vhds".  Azure
will create it for you automatically if it's missing, if you're using a
standard OS image, but we'll create it ourselves here:

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

Note that when a VM is using a VHD, it places a lease on the VHD so that
it cannot be deleted.  The reverse is not true, however: when you delete
a stopped VM, the resources it has been using, including VHDs are not
deleted (because they might be re-used subsequently by a new VM).  As a
result, be careful to check whether or not VHDs from deleted VMs need to
be kept, and delete them manually if need be.

VMs can also have data disks, of course.  The number of data disks that
can be mounted is a function of VM size.

## VHDs, Storage Accounts, IOPS, and Ingress/Egress Bandwidth

Generally, VHDs in Standard storage accounts have an [IOPS limit](https://docs.microsoft.com/en-us/azure/storage/storage-scalability-targets) of 500.
These accounts have a total limit of 20k IOPS, and dividing the latter by
the former gives the standard guidance of no more than 40 VHDs per account.
It's also true, however, that the 500 IOPS is not guaranteed - performance
degrades as this limit is approached.  Another wrinkle is that most disks
are not taxed at close to 500 IOPS most of the time.  So... the actual
number of VHDs that you can successfully put in a standard account has as
much to do with your usage patterns as it does with technology.  If you're
booting a lot of VMs simultaneously, you'll want fewer VHDs in those accounts.

When VMs are I/O bound against a VHD, they may block and become unresponsive.
If that VM is providing network services, these connections may correspondingly
lag or fail.  Keep in mind that when debugging what looks like a network
issue, you may actually be looking at a storage issue.

One way to mitigate an IOPS limit is to use a technique such as logical
volume management, to map a logical disk across several VHDs.  This is
recommended only for data disks, though, not boot disks.

VHDs in Premium storage are a different story.  They have IOPS and storage
bandwidth (ingress+egress) [limits](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) that change by the size of the disk.  As a
result, it's not possible to give useful general guidance on the number of
VHDs that can be put into a Premium account.
Because there is also a total IOPS and storage bandwidth limit based on the 
[type/size of the VM](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-sizes),
it is very possible with Premium storage for the VM to be the I/O bottleneck,
rather than the storage account.  

## Fault-Tolerance and VHDs

A storage account is mapped to a single storage "stamp".  Even if your VMs
are in an availability set and mapped across fault domains, if their VHDs
are all in a single storage account, its stamp is a single point of failure.
For maximum fault tolerance, make sure VMs in availability sets have their
VHDs in separate storage accounts.
