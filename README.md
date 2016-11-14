An Introduction to Microsoft Azure
==================================
Gaining technical proficiency with Azure, starting from scratch, can be a
daunting challenge.  Azure has been growing at a fast pace, in both breadth
and depth, since its inception - with documentation at the same scale.  Not
only do new features arrive frequently, but existing ones have changed and
continue to do so.  Finding your way through the very detailed
documentation online can feel like working on a jigsaw puzzle without knowing
in advance what the picture on the box is.

This introduction is designed to hit the Goldilocks point: fine-grained enough
so as to give you the technical grounding you need, but not so much that you're
getting lost in the weeds.  The major concepts are presented in a logical
order, covering all the prerequisites for working with linux virtual machines,
with short hands-on examples that demonstrate the material, but callouts to
the detailed docs if you want a deeper dive on that topic. 

# Bootstrap
Azure is a remote distributed collection of virtualized 
[resources](concepts/resources.md), and the interfaces and tools that 
allow you to securely manage and interact with them.

Fundamentally, these interfaces are REST APIs - HTTP requests are made to
initiate an operation, and the results are communicated back from Azure in
the consequent HTTP response.  While the REST APIs 
are great, they're not great for most humans to use directly, so various
other abstractions have been built on top of them.  There are SDKs and tools
for various languages, such as
[Java](https://azure.microsoft.com/en-us/develop/java/),
[Node.js](https://azure.microsoft.com/en-us/develop/nodejs/), and
[Python](https://azure.microsoft.com/en-us/develop/python/),
and more importantly for our purposes here, two command-line
interfaces: one in Powershell (now [open source and available for linux]
(https://azure.microsoft.com/en-us/blog/powershell-is-open-sourced-and-is-available-on-linux/)), and a cross-platform (xplat) CLI written in 
Node.js.  All of these ultimately communicate with Azure via REST, though.

Throughout this introduction, we will use the xplat CLI for the examples,
so you'll want to have that [installed]
(https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/)
before proceeding.  We assume you have already been set up with a subscription
(by your organization, or a [free personal one](https://azure.microsoft.com/en-us/free/)) as well.

# Flow

The example code in the sections that follow build on each other and are
meant to be executed in the sequence below.  If you skip around, you may
run into errors from unmet dependencies.  If that's your style, don't let
that stop you!  Figuring out why some example code doesn't work, and how
to fix it, is a great way to learn.  If you get stuck, though, you should
be able to start over from the beginning with a clean slate and make it
all work. 

# Table of Contents

## Concepts
* [Environments](concepts/environments.md)
* [Authentication & Authorization](concepts/auth.md)
* [Subscriptions](concepts/subscriptions.md)
* [Regions](concepts/regions.md)
* [Resources, Resource Providers, and Resource Groups](concepts/resources.md)
* [ARM Templates](concepts/templates.md)
* [Idiosyncracies, Gotchas, and Debugging Tips](concepts/debugging.md)

## Infrastructure as a Service
* [The Iaas "Big Three": Network, Storage, and Compute](iaas.md)

## Network
* [Virtual Networks & Subnets](network/vnets.md)
* [Gateways](network/gateways.md)
* [IP Addresses & NICs](network/ips_nics.md)
* [Network Security Groups](network/nsgs.md)
* [Load Balancers](network/lbs.md)

## Storage
* [Blobs, Tables, Queues, and Files](storage/types.md)
* [Premiere, Hot, and Cold](storage/bloblevels.md)
* [VHDs](storage/vhds.md)

## Compute


