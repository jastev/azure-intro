Idiosyncracies, Gotchas, and Debugging Tips
===========================================
## Versions
Each of the REST APIs for the resource providers are [independently versioned]
(https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-supported-services),
and applications (including the azure CLI) typically are hard-coded to use
a particular version of each.  It is likely the case with at least some of
these APIs, therefore, that you are not using the latest version - the
version described in the online documentation.  It also happens that two 
different versions of an application (again, including the azure CLI) can
be using two different versions of the REST API for a given resource provider,
with accordingly different behavior.

## Quotas
By default, an azure subscription has limits on the provisioning of certain
kinds of resources.  These [quotas](https://azure.microsoft.com/en-us/documentation/articles/azure-subscription-service-limits/) can be raised by contacting
Microsoft Support.

## CLI
There are several general switches to the Azure CLI which can be helpful:

* _-h_ gives context-sensitive help, including many rarely-used but helpful
options
* _-v_ generates verbose output
* _--json_ returns the results of the action as json, which often contains
information which would not be included in the more conventional response
text

## Local Logs
The ~/.azure/ directory contains various log files related to the CLI, 
including most notably azure.details.log and azure.err, with additional
information on successful and failed requests, respectively.

## ARM Activity Logs
azure group log show -n <resource-group-name> will give the history of the
ARM operations on a given resource group.  There are various other additional
options for this command that give more or less detail.

## Resource Explorer
https://resources.azure.com is an extremely powerful way to interact with
your subscriptions and resources via REST and JSON.
