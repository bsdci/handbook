---
title: NullFS Mounts
---
Every jail has a fstab file that stores mountpoints that are being mounted on Jail start.
Those files can be edited with the fstab file abstraction class that allows listing, creating and removing entries.

An instance of the [`ioc.Config.Jail.File.Fstab.Fstab`](https://ioc.github.io/libioc/ioc.Config.Jail.File.Fstab.html) class is available on a Jail object as `fstab` property that is lazy-loaded on access.

```python
>>> import ioc
>>> jail = ioc.Jail(dict(name="non-basejail", basejail=False), new=True)
>>> jail.create(ioc.Release("11.2-RELEASE"))
>>> jail.exists
True
>>> list(jail.fstab)
[{
    'source': '/iocage/jails/non-basejail/launch-scripts',
    'destination': '/iocage/jails/non-basejail/root/.iocage',
    'options': 'ro',
    'fstype': 'nullfs',
    'dump': '0',
    'passnum': '0',
    'comment': 'iocage-auto'
}]
```

The single entry of the non-basejail created above is automatically created by libioc in order to mount launch scripts for Jail startup and teardown.

### Adding Mountpoints

The Fstab class is a list of [`ioc.Config.Jail.File.Fstab.FstabLine`](https://ioc.github.io/libioc/ioc.Config.Jail.File.Fstab.html#ioc.Config.Jail.File.Fstab.FstabLine) entries.
New entries can be created by creating and adding such an object.

```python
jail = ioc.Jail("non-basejail")

new_line = ioc.Config.Jail.File.Fstab.FstabLine(dict(
  source="/tmp/my-mountpoint",
  destination="/mnt",
))

jail.fstab.add_line(new_line)
jail.fstab.save()
```

Note: Unlike the `{{site.ioc_cli_tool}} fstab add` sub-command the iocage Python module itself does not create the destination directory in the Jail.

### Removing Mountpoints

Fstab instances behave like usual list objects where entries can be removed using the `del` keyword.
Afterwards the changes can be written to the file.

```python
>>> jail = ioc.Jail("non-basejail")
>>> len(jail.fstab)
2
>>> del jail.fstab[1]
>>> len(jail.fstab)
1
>>> jail.fstab.save()
```