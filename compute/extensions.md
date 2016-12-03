VM Extensions
=============

[VM extensions](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-windows-extensions-features)
are small applications that run at boot and provide services such as 
configuration management, or features related to other Azure services
such as monitoring or security.  Management of extensions is provided
through the Azure Linux Agent and a variety of clients, such as the CLI.

```bash
# azure vm extension set -g <resource-group> -m <vm> -n <extension> -p <publisher> -o version --public-config-file <path>
# azure vm extension set -g <resource-group> -m <vm>  -n <extension> -p <publisher> -o version --public-config <json>
```

Extensions are removed with the -u or --uninstall option:

```bash
# azure vm extension set -g <resource-group> -m <vm> -n <extension> -p <publisher> -o version -u
```

One very useful extension is [CustomScript](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-extensions-customscript).
Within the configuration, you specify a file to run, and optionally a URN
from which to retrieve it at boot.

```bash
# azure vm extension set -g intro-rg -m intro-vm -n CustomScript -p Microsoft.Azure.Extensions -o 2.0 --public-config '{"fileUris": ["https://www.example.com/example.sh"],"commandToExecute": "./example.sh"}'
```
