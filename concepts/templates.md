ARM Templates
=============
A set of [Azure resources](resources.md) and their configured state can be
represented in the form of a JSON file called a template.  Templates are
declarative rather than imperative - in other words, they describe *what*
the resources should look like, rather than *how* to make them so.

To retrieve a template that models the current state of an existing resource
group, we use the azure CLI as follows:

```bash
# azure group export <resource-group>
# azure group export -n <resource-group>

$ azure group export -n intro-rg
info:    Executing command group export
+ Exporting resource group as template intro-rg                                
info:    Template downloaded to ./intro-rg.json
info:    group export command OK 
```

While this template has hard-coded values that correspond to the current
state of its resources, the template can then be paramaterized to model
these as variables.  The template and associated parameter file can then
be used to [deploy](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-cli) similar systems of resources elsewhere.

The resource group that the template is being deployed into must already
exist.

```bash
# azure group deployment create -g <resource-group> -f <template-file>

$ azure group deployment create -g intro-rg-2 -f intro-rg.json
info:    Executing command group deployment create
info:    Supply values for the following parameters
+ Initializing template configurations and parameters                          
+ Creating a deployment                                                        
info:    Created template deployment "intro-rg"
+ Waiting for deployment to complete                                           
+                                                                              
+                                                                              
data:    DeploymentName     : intro-rg
data:    ResourceGroupName  : intro-rg-2
data:    ProvisioningState  : Succeeded
data:    Timestamp          : Mon Nov 21 2016 13:47:26 GMT-0800 (PST)
data:    Mode               : Incremental
data:    CorrelationId      : 5582ca2a-8b2c-442e-83a6-1ca2beb2dbff
info:    group deployment create command OK
```

Note the Mode line.  Deployments can be made in two different ways, incremental
and complete.  The major difference between the two modes is that a complete
deployment will ensure that the resulting state of the resource group matches
the template completely, including deleting any previously existing resources
in the group that are not specified in the template.

The deployment will be given a default name unless it is specified with
the -n switch.

It is also possible to retrieve the template that was originally used to
populate a resource group, even though the current state of the resources
in the group may no longer match the original template.

```bash
# azure group deployment template download -g <resource-group> -n <deployment>

$ azure group deployment template download -g intro-rg-2 -n intro-rg
info:    Executing command group deployment template download
+ Getting resource group deployment template intro-rg               
info:    Deployment template downloaded to ./intro-rg.json
info:    group deployment template download command OK
```

