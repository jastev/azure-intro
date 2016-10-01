Environments
============
"Azure" is not one cloud, but many.  Most people will be using the Azure
Public Cloud, which is generally available around the world.
However, because of issues such as data sovereignty and national security,
"Azure" also comprises several sovereign clouds, each operating solely
within their respective national boundaries.

Microsoft is also introducing Azure Stack, in which the underlying cloud
software fabric can be run on private hardware within enterprise data
centers.  This makes possible the creation of arbitrary commercial Azure
clouds.

Each of these clouds must support being wholly self-contained.  Everything
from REST endpoints for [resource providers](resources.md) to
[AAD tenants](auth.md) must be provided within the cloud.  Each of these
clouds is referred to generically as an "environment".

Note that because each environment is under local control, not all resource
providers may be available, API versions may vary, etc.

The list of environments can be seen like this:

```bash
$ azure account env list
info:    Executing command account env list
data:    Name             
data:    -----------------
data:    AzureCloud       
data:    AzureChinaCloud  
data:    AzureUSGovernment
data:    AzureGermanCloud 
info:    account env list command OK
```

You can obtain the technical details for a given environment, for example
whether or not it is public, and its REST endpoints, as follows:

```bash
# azure account env show <environment-name>
# azure account env show --environment <environment-name>

$ azure account env show AzureCloud
info:    Executing command account env show
data:    Name:                                              AzureCloud
...
data:    isPublicEnvironment:                               true
...
data:    managementEndpointUrl:                             https://management.
                                                            core.windows.net
...
```
