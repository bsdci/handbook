Every jail has a fstab file that stores mountpoints that are being mounted on Jail start.
Those files can be edited with the fstab file abstraction class that allows listing, creating and removing entries.

An instance of the [`iocage.Config.Jail.File.Fstab.Fstab`](https://iocage.github.io/libiocage/iocage.Config.Jail.File.Fstab.html) class is available on a Jail object as `fstab` property that is lazy-loaded on access.

```python
>>> import iocage
>>> jail = iocage.Jail(dict(name="non-basejail", basejail=False), new=True)
>>> jail.create(iocage.Release("11.2-RELEASE"))
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

The single entry of the non-basejail created above is automatically created by libiocage in order to mount launch scripts for Jail startup and teardown.

### Adding Mountpoints

The Fstab class is a list of [`iocage.Config.Jail.File.Fstab.FstabLine`](https://iocage.github.io/libiocage/iocage.Config.Jail.File.Fstab.html#iocage.Config.Jail.File.Fstab.FstabLine) entries.
New entries can be created by creating and adding such an object.

```python
jail = iocage.Jail("non-basejail")

new_line = iocage.Config.Jail.File.Fstab.FstabLine(dict(
  source="/tmp/my-mountpoint",
  destination="/mnt",
))

jail.fstab.add_line(new_line)
jail.fstab.save()
```

Note: Unlike the `{{site.iocage_cli_tool}} fstab add` sub-command the iocage Python module itself does not create the destination directory in the Jail.

### Removing Mountpoints

Fstab instances behave like usual list objects where entries can be removed using the `del` keyword.
Afterwards the changes can be written to the file.

```python
>>> jail = iocage.Jail("non-basejail")
>>> len(jail.fstab)
2
>>> del jail.fstab[1]
>>> len(jail.fstab)
1
>>> jail.fstab.save()
```