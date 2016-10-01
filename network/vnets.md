Virtual Networks & Subnets
==========================
Azure [virtual networks](https://azure.microsoft.com/en-us/documentation/articles/virtual-networks-overview/)
or "vnets" are logically independent of each other - you can have the same
or overlapping address space in two different vnets.

```bash
azure network vnet create -g <resource-group> -n <name> -l <location> -a <address-space>
```

```bash
azure network vnet list -g <resource-group>
```

```bash
azure network vnet show -g <resource-group> -n <vnet-name>
```

## Subnets

```bash
azure network vnet subnet create -g <resource-group> -e <vnet-name> -n <name> -a <address-space>
```

```bash
azure network vnet subnet list -g <resource-group>
```

```bash
azure network vnet subnet show -g <resource-group> -n <subnet-name>
```

