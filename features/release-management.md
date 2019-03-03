---
title: Release Management
---
Distribution releases in iocage provide the userland environment for Jails.
While the Kernel is shared with host environment the userland is independently downloaded (and updated) from the distribution mirrors.
This process is abstracted by libioc by providing methods that trigger management tasks connected to such releases.

### Finding Releases

The libioc [Distribution](https://ioc.github.io/libioc/ioc.Distribution.html) object has a dynamic property called `releases`.
When requesting this property, the list of remotely available releases is pulled from the host distributions mirror and compared to End-Of-Life announcements.

```python
>>> import ioc
>>> distribution = ioc.Distribution()
>>> [release.name for release in distribution.releases]
[
    '9.3-RELEASE',
    '10.1-RELEASE',
    '10.2-RELEASE',
    '10.3-RELEASE',
    '10.4-RELEASE',
    '11.0-RELEASE',
    '11.1-RELEASE',
    '11.2-RELEASE'
]
```

### Fetching Releases

iocages traditional method of obtaining releases is the `fetch` comamnd of command-line tool.

When no specific release name is specified as command argument, an interactive list of options is printed to the CLI user.

```sh
> {{site.ioc_cli_tool}} fetch
[0] 9.3-RELEASE (EOL)
[1] 10.1-RELEASE (EOL)
[2] 10.2-RELEASE (EOL)
[3] 10.3-RELEASE (EOL)
[4] 10.4-RELEASE
[5] 11.0-RELEASE (EOL)
[6] 11.1-RELEASE
[7] 11.2-RELEASE (Newer than Host)
Type the number of the desired RELEASE
Press [Enter] to fetch the default selection (11.1-RELEASE) [5]:
```

Releases newer than the host are unsupported and marked as such.
The same applies to releases that were no longer supported by the distributions maintainers.
Either information can be found in braces after the release names - users with releases marked as both `EOL` and `Newer than Host` are strongly advised to update the host.

Internally the CLI tool `{{site.ioc_cli_tool}}` uses the iocage Python module similar to this snippet:

```python
>>> import ioc
>>> distribution = ioc.Distribution()
>>> len(distribution.releases)
7
>>> release = distribution.releases.pop()
>>> release.name
'11.2-RELEASE'
```

Each Release exposes a `fetch()` method that triggers the process of downloading it:

```python
release = ioc.Release("11.2-RELEASE")
release.fetch()
```

Note: The fetch method will return a Generator when being called on ReleaseGenerator.
The simplified class [`Release`](https://ioc.github.io/libioc/ioc.Release.html) used in the snippet above does only return a list at once after it finished the entire download/update.

### EOL and Host Incompatibility

Releases have each dynamic `eol` and `newer_than_host` properties that indicate the support status.

```python
>>> import ioc
>>> host = ioc.Host()
>>> host.release_version
'11.1-RELEASE'
>>> distribution = ioc.Distribution()
>>> [(x.name, x.eol, x.newer_than_host) for x in distribution.releases]
[
    ('9.3-RELEASE', True, False),
    ('10.1-RELEASE', True, False),
    ('10.2-RELEASE', True, False),
    ('10.3-RELEASE', True, False),
    ('10.4-RELEASE', False, False),
    ('11.0-RELEASE', True, False),
    ('11.1-RELEASE', False, False),
    ('11.2-RELEASE', False, True)
]
```

### Release Management Security

Secure downloads and updates of release assets are one of the design goals of libioc.
Neither the connection to the distribution mirror, nor any data that could have been modified within a jail are trusted.

### Patches

Releases downloaded from a distribution mirror already contain the latest version including patches.
Therefore newly created releases are tagged as patchlevel `0` for both FreeBSD and HardenedBSD.
The next time an existing release is fetched, the distributions update tool is used to pull patches offline and to apply them to Releases or non-basejails.
After changes occur, libioc reads the applied patch level, that is then reflected in the new snapshot.

It is not possible to download older releases of outdated patchlevels because the distribution mirror already includes the most recent updates.
Jails that get migrated from hosts with snapshots of releases must be updated to be migrated to the new host.

While libioc assumes to use the snapshot of the latest available patches, it is possible to target specific versions (for example `11.1-RELEASE-p4`).