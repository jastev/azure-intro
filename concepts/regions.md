Regions
=======
Azure datacenters are distributed around the world in what are called
"regions" (though the parameter name in the xplat cli is sometimes
misleadingly labelled "location").  Even when there are two datacenters
in the same proximal geographic area (e.g. East US and East US 2, both
in Virginia), they are considered separate regions.

Some Azure resources are hardware-dependent, and this specialized hardware
might not be available in all regions at any given time.  But also, new
resource providers and changes to existing ones do not roll out to
all regions in the Azure Public Cloud simultaneously.  Similarly, sovereign
clouds might choose to delay adoption of an update, or an enterprise cloud
might incorporate a third-party resource provider not generally available
publically.  For all or any of these reasons, resource providers vary from 
region to region.

You can find all of the regions/locations for the currently active
subscription with:

```bash
azure location list
```

Note that regional names are formed by concatenating the country name, 
a geographic modifier, and a disambiguating numeric suffix (if required).
However, the older the region, the more likely it is that the geographic
modifier will precede the country name, rather than the reverse.  Hence,
we have regions like westus and westus2, but japaneast and japanwest.
