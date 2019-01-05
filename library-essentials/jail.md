---
title: Jail
---
An iocage Jail is a data structure similar to a [Release](#release).
In addition to its root dataset cloned from a Release the Jail structure at least contains the Jail Configuration and an fstab file that differentiate the Jail from its Release.

```
/iocage/jails/<JAIL_NAME>/
/iocage/jails/<JAIL_NAME>/root
/iocage/jails/<JAIL_NAME>/fstab
/iocage/jails/<JAIL_NAME>/config.json
```

In terms of FreeBSD a jail is defined in the kernel as "prison structure that exists until the last inmate is dead."
Usually this refers to the last process assigned to a jail, but iocage starts jails with the `persist` option, so that we manually tear down a running jail.
Each running jail on FreeBSD has a JID assigned that signals the running state.

#### Jail State

Every Jail instance has a `Jail.state` propery that is an optional dictionary reflects the output of the [jls command](https://www.freebsd.org/cgi/man.cgi?query=jls).
Virtual properties like `Jail.running` and `Jail.stopped` provide (read only) boolean values about the jail state.

```python
>>> jail = iocage.Jail("myjail")
>>> jail.running
True
>>> isinstance(jail.state, iocage.JailState.JailState)

>>> jail.stop()
...

>>> jail.running
False
>>> jail.stopped
True
>>> (jail.state is None)
True
```

Several operations on a Jail can only be applied when it has a specific state, so that it is not possible to start a jail that is already running.

#### Jail Creation

When creating a Jail the `new` argument disables the internal checks for existence of the jail data structure.
Instead it is created in the next step from a fetched Release.

```python
release = iocage.Release("{{site.current_fbsd_release}}")
jail = iocage.Jail(data(name="myjail"), new=True)
jail.create(release)
```

Even when enabling the `new` argument, a jail specified via `id` is required to exist. A Jail initialized with a name attempts to claim the id on creation on a specific ioc dataset using [multiple source datsets](https://github.com/bsdci/libioc/pull/287).
