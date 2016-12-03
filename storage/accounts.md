Storage Accounts
================

To use the service, you must first create one or more storage accounts 
within your subscription.  There are different [kind of accounts](https://azure.microsoft.com/en-us/documentation/articles/storage-introduction/), and
each has various subtypes and configuration options.  Many of these are
mutually incompatible, and cannot be changed after the account has been
created, so it is important to be aware of the design impact of your choices
from the beginning.

## General-Purpose Accounts

By default, a storage account is general purpose and can be used to store
any of the four kinds of data.  This kind of storage account has two
performance tiers, standard and premium.

Standard accounts are backed by mechanical disks, and support fewer (20k) "I/O
Operations Per Second" ([IOPS](https://docs.microsoft.com/en-us/azure/storage/storage-scalability-targets)).  The specs for standard accounts are not
guaranteed; as they are approached the storage service will begin returning
HTTP status codes of 503 (Server Busy) or 500 (Operation Timeout).

At creation time, you can choose the kind of data replication you need for
the standard account: locally redundant storage (LRS) makes multiple copies
within the same data center; zone redundant storage (ZRS) makes copies in
two paired datacenters that are usually geographically "close"; globally
redundant storage (GRS) makes copies in datacenters around the world.
Note that ingress/egress bandwidth is always greatest for LRS, and greater
in the US than in Europe/Asia.

[Premium](https://azure.microsoft.com/en-us/documentation/articles/storage-premium-storage/) accounts, on the other hand, are
backed by SSDs and have a higher IOPS limit.  However, currently, premium
accounts are not truly "general purpose" - they *only support Azure VHDs
(page blobs) for VMs*.  And additionally, not every size of Azure VM can
use a premium image - only DS, DSv2, GS, or FS.  Premium accounts also only
support LRS, and do not support [public accessibility](accessibility.md).

Unlike standard accounts, which have the same stats regardless of the data they contain, 
premium accounts come in three sizes: P10 (128GiB), 
P20 (512GiB), and P30 (1024GiB).  Your actually used size is rounded up to
the nearest Premium level (there's no need to explicitly provision a certain
level, or move from one to another).  Each comes with fully committed
performance specs, graduated with those levels.

## Blob Accounts

Instead of a general purpose account, you can choose to create
a blob storage account.  As the name implies, these accounts can only store
blobs - however, they can *only store block and append blobs, not page
blobs*.  As a result, they are *not able to store disk images*.

Similar to the performance tier of a general purpose storage account, blob
storage accounts expose two [access tiers](https://azure.microsoft.com/en-us/documentation/articles/storage-blob-storage-tiers/): hot and cool.  Hot is the
default, and is optimized for frequently accessed data.  Cool is optimized
for infrequent accesses - storage costs themselves are cheaper, but access
costs are more expensive.

## CLI

```bash
# azure storage account create -g <resource-group> --kind <kind> --sku-name <sku> --access-tier <tier> -l <location> <account-name>
$ azure storage account create -g intro-rg --kind Storage --sku-name LRS -l westus intro20161122strg
info:    Executing command storage account create
+ Checking availability of the storage account name                            
+ Creating storage account                                                     
info:    storage account create command OK
```

A few tips to note...  First, be aware that unlike other resource names, 
storage account names must be globally unique, contain only lower case letters
and digits, and be between 3 and 24 characters long.  One good way to help
satisfy this is to use a datestring of the form YYYYMMDD within the name.
Second, note that unlike many CLI commands, the -n switch to specify the name
of the account is not supported. 

Premium accounts are created using the 'PLRS' sku:

```bash
$ azure storage account create -g intro-rg --kind Storage --sku-name PLRS -l westus intro20161122strgprm
info:    Executing command storage account create
+ Checking availability of the storage account name                            
+ Creating storage account                                                     
info:    storage account create command OK
```
