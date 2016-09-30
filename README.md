An Introduction to Microsoft Azure
==================================
Gaining technical proficiency with Azure, starting from scratch, can be a
daunting challenge.  Azure has been growing at a fast pace, in both breadth
and depth, since its inception - with documentation at the same scale.  Not
only do new features arrive frequently, but existing ones have changed and
continue to do so.  Finding your way through the very detailed
documentation online can feel like working on a jigsaw puzzle without knowing
in advance what the big picture is.

This introduction is designed to hit the Goldilocks point: fine-grained enough
so as to give you the technical grounding you need, but not so much that you're
getting lost in the weeds.  The major concepts are presented in a logical
order, covering all the prerequisites for working with virtual machines,
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
other abstractions have been built on top of them.  There are SDKs for various
languages, and more importantly for our purposes here, two command-line
interfaces: one in Powershell, and a cross-platform (xplat) CLI written in 
Node.js.  All of these ultimately communicate with Azure via REST, though.

Throughout this introduction, we will use the xplat CLI for the examples,
so you'll want to have that [installed]
(https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/)
before proceeding.  We assume you have already been set up with an account
by your organization, as well.

# Table of Contents
## Concepts
* [Environments](concepts/environments.md)
* [Authentication & Authorization](concepts/auth.md)
* [Subscriptions](concepts/subscriptions.md)
* [Regions](concepts/regions.md)
* [Resources, Resource Providers, and Resource Groups](concepts/resources.md)
* [Idiosyncracies, Gotchas, and Debugging Tips](concepts/debugging.md)

## Network


## Storage


## Compute


