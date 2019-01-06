---
title: Compatibility
---
Over the years iocage evolved from a shell script to an implementation using python.
libiocage unites all prior variants of iocage and iocell (the fork from iocage_legacy), so that it can deal with all configuration storage variants.

### iocage variants

| Name                      | Config Storage Format |
|---------------------------|-----------------------|
| iocage_legacy#1.7.6       | ZFS Properties        |
| iocage_legacy#develop     | UCL file              |
| python-iocage             | JSON file             |
| libiocage                 | All formats           |

### Automatic format detection

libiocage prefers storing Jail configuration in the most recent format, a JSON file.
When running it on a host with existing Jails, the libiocage attempts to read their config in the following order:

1. JSON
2. UCL
3. ZFS Properties

New Jails will be created in the most recent format found on a host, so that it is possible to use libiocage in combination with any of the above iocage variants.

### Migration between config formats

The current config format of a Jail is stored in the Jail.config_format property. It can be either `json`, `ucl`, `zfs` or `auto` when it was not yet detected.
The next time a Jail config is saved, the selected format will be used.

This snippet demonstrates the migration from JSON to ZFS properties:

```python
>>> import ioc
>>> jail = ioc.Jail("some-jail")
>>> jail.config_format
'json'
>>> jail.config_format = 'zfs'
>>> jail.save()
```

The above operation does not delete the prior configuration (JSON), so that the Jail config will still be read from its JSON config - deleting the prior configuration resolves this issue.
Of course the `{{site.ioc_cli_tool}} migrate` takes care by cleaning up after a successful migration to the most recent format.

### What about features?

libiocage aims to be feature complete with other variants, so that any missing features will be tracked as [compatibility issue](https://github.com/iocage/libiocage/issues?q=label%3Acompatibility) on GitHub. There might be new features that libiocage stores in nested JSON properties that cannot be migrated to prior formats. This is only the case for features that were newly introduced by libiocage, so that conflicts are unlikely.