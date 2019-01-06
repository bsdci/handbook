---
title: "Migration"
---

Switching to the latest Jail Config format has performance benefits and enables features that have been introduced in libioc.

As usual, libiocage treats operations that modify existing jails with caution.
As precaution a snapshot is automatically created before migrating a jail, so that jails are never left in a defect state.

Note: Jails that have been migrated might no longer been seen by iocage_legacy.

### Migration with the CLI tool

```sh
{{site.ioc_cli_tool}} migrate my-jail
```

### Migration with the Python Module

The programmatic migration of jails can be achieved by changing a jails configuration format and renaming its dataset.

While the [implementation of the ioc CLI tool] is a great reference for migration with the Python Module, a simplified procedure can be broken down to

1. Renaming the Jails Dataset
2. Updating the Jail Config format

```python
import ioc
jail = ioc.Jail("0BAD1DEA")  # short UUID
jail.rename(jail.tag)
jail.config_type = "json"
jail.save()
```

### Remarks / Restrictions

- It is not possible to migrate jails at runtime
- A jails tag must comply with the naming scheme
- The resulting dataset must not be claimed by another jail
