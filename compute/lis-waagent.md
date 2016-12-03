Linux Integration Services (LIS)
================================

Azure VMs run on Microsoft's Hyper-V virtualization hypervisor.  In order
to operate smoothly and provide the best performance, these VMs need
Hyper-V-specific code in the Linux Kernel.  This codebase is open source,
but actively contributed to by Microsoft, and is called Linux Integration
Services.

LIS goes through the regular process of being submitted to the linux kernel
maintainers, and newer kernels will thus contain newer versions of LIS.
Microsoft also works with the major distros to backport LIS to the kernel
versions that they are shipping.  Thus, depending on the source and age
of the boot image and its kernel, [different versions of LIS](https://blogs.technet.microsoft.com/virtualization/2016/07/12/which-linux-integration-services-should-i-use-in-my-linux-vms/)
 may be in play in different VMs.

As later versions of LIS both enable new features, improve existing ones,
and correct defects, it's important to keep LIS in mind when troubleshooting
a linux VM in Azure.

Azure Linux Agent (waagent)
===========================

The [Azure Linux Agent](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-agent-user-guide)
is another important part of the integration of Linux VMs into Azure.  It
has several important roles at provisioning-time, boot-time, and run-time,
including setting up the initial user account and ssh access, mounting
local storage, configuring swap, redirecting the serial port for diagnostics,
and setting up [VM extensions](extensions.md).

It is configured in /etc/waagent.conf.  Note that Ubuntu images use
[cloud-init](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-using-cloud-init) for many provisioning-time tasks, and this 
requires particular settings in waagent.conf.
