---
title: Release
---
FreeBSD jails allow to isolate software while sharing kernel resources across the system.
iocage runs the entire userland of a distribution release that it has downloaded from a FreeBSD or HardenedBSD release server.
In the world of iocage every release is represented as a zfs dataset stored in `<ZPOOL>/iocage/releases/<RELEASE_NAME>` that be cloned into a [Jail](#jail) later on.

Releases are provided by the OS distribution maintainers and can be listed from the remote server.

```python
>>> import iocage
>>> distribution = iocage.Distribution()
>>> [x.name for x in distribution.releases]
[
    '9.3-RELEASE'
    '10.1-RELEASE',
    '10.2-RELEASE',
    '10.3-RELEASE',
    '10.4-RELEASE',
    '11.0-RELEASE',
    '11.1-RELEASE',
    '11.2-RELEASE',
]
```

Attributes on a Release instance indicate the local availability and status of a release.

```python
>>> r = iocage.Release("10.1-RELEASE")
>>> r.available
True
>>> r.fetched
False
>>> r.eol
True
```

Releases that are newer than the host kernel are not officially supported because they might be subject to breaking ABI changes.

```python
>>> host = iocage.Host()
>>> host.userland_version
11.1
>>> release = iocage.Release("11.2-RELEASE")
>>> release.newer_than_host
True
```

To make a Release become available locally, it can be fetched from the remote servers.
During this process the release asset `base.txz` will be fetched and extracted.
When fetching an already existing release `freebsd-update`, respectively `hbsd-update` are used to apply the latest patches to the release.

Note: The execution time of `Release.fetch()` depends on the connection speed and extraction performance.

```python
>>> release = iocage.Release("10.1-RELEASE")
>>> release.fetched
False
>>> release.fetch()
[
    <iocage.events.FetchRelease object at 0x8086bd5f8>,
    <iocage.events.ReleasePrepareStorage object at 0x8086bd630>,
    <iocage.events.ReleasePrepareStorage object at 0x8086bd630>,
    <iocage.events.ReleaseDownload object at 0x8086bd668>,
    <iocage.events.ReleaseDownload object at 0x8086bd668>,
    <iocage.events.ReleaseExtraction object at 0x8086bd6a0>,
    <iocage.events.ReleaseExtraction object at 0x8086bd6a0>,
    <iocage.events.FetchRelease object at 0x8086bd5f8>,
    <iocage.events.ReleaseConfiguration object at 0x8086bd6d8>,
    <iocage.events.ReleaseConfiguration object at 0x8086bd6d8>,
    <iocage.events.ReleaseCopyBase object at 0x8086bd710>,
    <iocage.events.ReleaseCopyBase object at 0x8086bd710>
]
>>> release.fetched
True
```

For comfort and security reasons release updating is divided in two stages.
While updates are fetched by executing the updater utility to download a releases latest patches, they only make contact with a releases dataset during their application calling the `release.updater.apply()` method.

Note: Both methods `fetch()` and `apply()` return Python Generators, that covered later in this handbook.
Casting a generator to a list is not the most effective way to interface with it, but is the simplest way to consume it visually.

```python
>>> list(release.updater.fetch())
[
    <iocage.events.ReleaseUpdatePull object at 0x80870fc50>,
    <iocage.events.ReleaseUpdatePull object at 0x80870fc50>,
    <iocage.events.ReleaseUpdateDownload object at 0x8086b4c88>,
    <iocage.events.ReleaseUpdateDownload object at 0x8086b4c88>
]
>>> list(release.updater.apply())
[
    <iocage.events.RunResourceUpdate object at 0x8086b40b8>,
    <iocage.events.ExecuteResourceUpdate object at 0x80872b860>,
    <iocage.events.JailResolverConfig object at 0x80872b7b8>,
    <iocage.events.JailResolverConfig object at 0x80872b7b8>,
    <iocage.events.JailLaunch object at 0x80872b518>,
    <iocage.events.JailLaunch object at 0x80872b518>,
    <iocage.events.ExecuteResourceUpdate object at 0x80872b860>,
    <iocage.events.RunResourceUpdate object at 0x8086b40b8>,
    True
]
```