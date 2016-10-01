Resources, Resource Providers, and Resource Groups
==================================================

## Resources

An Azure resource is a discrete element of functionality that can usually
be independently provisioned, managed, and released; it generates costs
according to a specific metric, according to use, independently of other
resources.  In short, whatever you're building in Azure, resources are
what you're building it with, and determine how much you'll pay for it.

Every resource in Azure is provisioned as part of a [subscription]
(subscriptions.md) within a specific [region/location](regions.md).

## Resource Providers

The entire lifecycle of a resource, and all interactions with it,
is governed by its resource provider (RP) and resource type.  A single 
RP can expose multiple resource types, but a given resource can only be
of a single type.  The provider and type exclusively determine the
legal means and meaning for interactions with the resource.

RPs are implemented as REST APIs.  When a request to provision a new
resource is received, the RP returns the resource's unique id on success.
Subsequent manipulation of the resource goes through the RP's REST API,
with the id as a parameter.  Additional information associated with the
request is typically passed in through JSON in the request body.  Data
in the response is also returned as JSON.

The REST APIs for all RPs are versioned - the desired version is explicitly
specified in the call.  RP versions have their own lifecycle, arriving in
a preview state, being promoted to GA, then chosen as the default
version by various interfaces, before being overtaken by newer versions,
deprecated, and finally obsoleted.  It is the normal case that [multiple
versions](debugging.md) of a given RP are concurrently available.

Each RP is registered in Azure with a namespace, which also uniquely
identifies it.  Because RPs can also be provided by third parties, these
namespaces begin with the vendor of origin.  Thus, the first party RPs
provided by Microsoft have namespaces such as Microsoft.Network,
Microsoft.Storage, and Microsoft.Compute

```bash
$ azure provider list
data:    
...
data:    ProviderNamespace :  Microsoft.Compute
data:    RegistrationState :  Registered
data:    ResourceTypes     :  availabilitySets, virtualMachines, virtualMachines/extensions, virtualMachineScaleSets...
data:    Locations         :  East US, East US 2, West US, Central US...
...
info:    provider list command OK
```

For a specific provider, you can get the list of the resource types that 
it supports, and the regions where each is available.

```bash
# azure provider show <provider-namespace>

azure provider show Microsoft.Compute
data:    
...
data:    ProviderNamespace :  Microsoft.Compute
data:    RegistrationState :  Registered
data:    ResourceTypes     :  virtualMachines
data:    Locations         :  East US, East US 2, West US, Central US, North Central US, South Central US, North Europe, West Europe, East Asia, Southeast Asia, Japan East, Japan West, Australia East, Australia Southeast, Brazil South, South India, Central India, West India, Canada Central, Canada East, West US 2, West Central US, UK South, UK West
data:    Aliases           : 
data:           Microsoft.Compute/virtualMachines/sku.name
data:           Microsoft.Compute/virtualMachines/imagePublisher
data:           Microsoft.Compute/virtualMachines/imageOffer
data:           Microsoft.Compute/virtualMachines/imageSku
data:           Microsoft.Compute/virtualMachines/imageVersion
...
data:    
info:    provider show command OK
```

## Resource Groups

Resources are often so fine-grained that their value comes from 
being combined with other resources, and it is helpful to organize them
accordingly.  Because these resources are conceptually
part of a single solution, they typically have the same lifecycle as well.
For these reasons, Azure allows bundling arbitrary resources into a
resource group.

A resource group is itself a resource, and like all resources, it has to
be created in a given location:

```bash
# azure group create <group> <location>
# azure group create -n <group> -l <location> --subscription <subscription-id>

$ azure group create -n intro-rg -l westus
info:    Created resource group intro-rg
data:    Id:                  /subscriptions/<subscription-id>/resourceGroups/intro-rg
data:    Name:                intro-rg
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    
info:    group create command OK
```

However, the resource group can contain resources that are **not** in the
same region.

You can see all of the resource groups for the current subscription with

```bash
$ azure group list
info:    Executing command group list
+ Listing resource groups                                                      
data:    Name       Location  Provisioning State  Tags:
data:    ---------  --------  ------------------  -----
data:    intro-rg   westus    Succeeded           null 
info:    group list command OK
```

and all of the resources within a group in the current subscription with

```bash
# azure group show <group>

$ azure group show intro-rg
info:    Executing command group show
+ Listing resource groups                                                      
+ Listing resources for the group                                              
data:    Id:                  /subscriptions/<subscription-id>/resourceGroups/intro-rg
data:    Name:                intro-rg
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:    Resources:  []
data:    Permissions:
data:      Actions: *
data:      NotActions: 
data:    
info:    group show command OK
```

or

```bash
# azure resource list -g <group>

$ azure resource list -g intro-rg
info:    Executing command resource list
+ Listing resources                                                            
info:    No matched resources were found.
info:    resource list command OK
```

These commands also take the "--subscription <subscription-id>" switch
to specify an alternate subscription.
