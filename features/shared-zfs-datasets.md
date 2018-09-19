---
title: Shared ZFS Datasets
---
FreeBSD ZFS datasets can be *jailed*, which removes them from the hosts visibility but allows them to be seen by a jail they are assigned to.

The first step of giving a jail access to a ZFS filesystem is to enable the `jailed` property.

```sh
zfs create zroot/my-jail-dataset
zfs set jailed=on zroot/my-jail-dataset
```

After this preparation iocage may automatically assign it to the jail on launch.

```sh
{{site.iocage_cli_tool}} set jail_zfs_dataset=zroot/my-jail-dataset my-jail
```

Note: Like other Jail Config list properties, multiple datasets can be specified comma separated.

Doing the same in Python requires to set a list structure containing the target jails datasets

```python
import iocage
jail = iocage.Jail("my-jail")
jail.config["jail_zfs_dataset"] = ["zroot/my-jail-dataset"]
jail.save()
```

Because of the `jailed=on` ZFS dataset property the dataset is not returned to the host after the jail was stopped.

It is not possible to assign a dataset to multiple jails.