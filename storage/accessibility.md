Accessibility
=============

In order to manipulate a storage account (as opposed to data
contained within it), you must have its [connection string](https://docs.microsoft.com/en-us/azure/storage/storage-configure-connection-string).

```bash
# azure storage account connectionstring show -g <resource-group> <account-name>

$ azure storage account connectionstring show -g intro-rg intro20161122strg
info:    Executing command storage account connectionstring show
+ Getting storage account keys                                                 
data:    connectionstring: DefaultEndpointsProtocol=https;AccountName=intro20161122strg;AccountKey=7l1tnfBNXsSv9jmdAsdvC1x0PMz9/jQNaAiyDTUuT94unQcdB/lOeksNYwDTOj9iKWErYA4WISbh0gV74qPu9w==
info:    storage account connectionstring show command OK
```

The connection string includes an account key; this is similar to a root
password.  The storage account has both a primary and a secondary account
key.  Anyone who has one of these keys has full access to the account.  If
either key is exposed, it should be replaced immediately:

```bash
# azure storage account keys renew -g <resource-group> --primary|--secondary <account-name>

$ azure storage account keys renew -g intro-rg --primary intro20161122strg
info:    Executing command storage account keys renew
+ Renewing storage account key                                                 
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  FNZLj/RyQkMB9aqnJn1iZf0HvBxj194TzyI4XlzG+5K2wG+7vbXwKlqGKQpB+vdXjTF72HFJ6yGR5E/6Xww2Ew==  Full       
data:    key2  Ecj0Kvh1e2JIvvkalJD1sKlwOIQAObA0kNa0xL6MIZzeaFIiLp0c0di1sNCcqYS4U8y6ybF+8QJpF52Ly2hhtQ==  Full       
info:    storage account keys renew command OK
```

Instead of keys, access can also be managed using [shared access signatures]
(https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1).
These are more fine-grained, and provide access to one or more service
endpoints within the account, rather than to the account as a whole.

It is also possible to set public accessibility at the container or the blob
level:

```bash
# azure storage container set -c <connection-string> -p Container|Blob <container-name>
```

At present, however, containers in premium storage accounts do not support public accessibility.  
