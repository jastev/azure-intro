Subscriptions
=============
A subscription is the root node of a resource tree.  It is also the single
point of billing information for usage associated with all resources in the
tree.  Each subscription is associated with only one [AAD tenant]
(auth.md) (which represents the owning organization), but an organization
may have any number of subscriptions.  More on the relationship between
AAD and subscriptions is available [here](https://azure.microsoft.com/en-us/documentation/articles/active-directory-how-subscriptions-associated-directory/).

Because subscriptions form the root of resource trees, they are the highest
level at which authorization can be granted to users.  It is not uncommon
for an organization to have users who are not granted authorization to all
of its subscriptions - each user may see only a subset of all the subscriptions
associated with their AAD tenant.

You can see all of the subscriptions to which your currently-authenticated
account has access as follows:

```bash
azure account list
```

Note that because subscriptions are tied to single AAD tenants, which are
in turn tied to specific [environments](environments.md), you will only
see the subscriptions available to you within a specific environment. 

When you login, your cli session will be associatiated automatically with
your default subscription.  To change the default, use:

```bash
azure account set <subscription-id>
```
