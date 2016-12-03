Local (Temporary) Storage
=========================
Azure VMs are provisioned with access to local disk.  This is available
for linux VMs under /dev/sdb1 and mounted by default to /mnt/resources.
The size of the temporary storage begins at 20Gb for the smallest VMs and
increases with VM size.  Local storage is high-throughput, low-latency,
and free.

Unlike the Azure Storage service, the local disk has no guarantee of 
persistence across VM reboots (where it might move to a different host).
It is well-suited to high-throughput, low-latency needs that are transient.
One common use is for swap (in fact, the [Azure Linux Agent](../compute/lis-agent.md) does this by default); another is for intermediate state on restartable
tasks (e.g. locally caching source code and compiling it).
