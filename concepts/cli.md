Cross-Platform (xPlat) CLI
==========================

## Logging In

Before you can begin interacting with Azure through the CLI, you will have to
[login](https://docs.microsoft.com/en-us/azure/xplat-cli-connect):

```bash
$ azure login
info:    Executing command login
info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code B8U7S2E5N to authenticate.
```

On the web page you will enter the code you're given by the CLI, and then
be prompted to authenticate.  After successfully authenticating, the CLI
will obtain a time-limited token that will allow it access to Azure with
your credentials.

If multifactor authentication is not in play, you can also use the -u
switch to login with a particular username, and the CLI will prompt you
for the password.

## Setting ARM Mode

Interaction with Azure was originally through the concept of services.  This
is now deprecated, and new usage should be through Azure Resource Manager (ARM).
However, legacy deployments require using service mode, so the CLI supports
both.

We will only cover ARM mode here, so set the CLI appropriately:

```bash
$ azure config mode arm
info:    Executing command config mode
info:    New mode is arm
info:    config mode command OK
``` 

## Syntax

The CLI syntax is generally of the form

```bash
$ azure <noun-phrase> <verb> <arguments-list>
```

The noun-phrase is one or more ordered words that uniquely describe the 
particular Azure feature to be used.

The verb is drawn from the following:
* list
* create
* show
* set
* delete

The arguments list can usually be supplied in two different styles.  In
the positionally-dependent style, the optional arguments appear first in
the list, followed by the mandatory arguments in a prescribed order.
I prefer the explicit-argument style, where all of the arguments are preceded
by switches telling the CLI what parameters they apply to.  In this case,
order is not important.

## Context-Sensitive Help

You can add the "-h" or "--help" switch to get context-sensitive help.  Added
to the noun phrase, it will give you additional options for extending it.
Added after the verb, it will give full usage information, including listing
all of the parameters.

## Version 2.0 Preview

[Version 2.0](https://github.com/Azure/azure-cli) of the CLI is in preview.
It is a complete rewrite (in Python rather than Node.js) and is not
syntax-compatible with the current Azure xPlat CLI.
