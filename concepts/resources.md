Resources, Resource Providers, and Resource Groups
==================================================

## Resources

An Azure resource is a discrete element of functionality that can usually
be independently provisioned, managed, and released; it generates costs
according to a specific metric, according to use, independently of other
resources.  In short, whatever you're building in Azure, resources are
what you're building it with, and determine how much you'll pay for it.

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
azure provider list
```

```bash
azure provider show Microsoft.Compute
```

## Resource Groups

Resources are often so fine-grained that their value comes from 
being combined with other resources.
