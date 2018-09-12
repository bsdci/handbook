Every Jail in iocage has a `config` property that points to its JailConfg instance. This object behaves like a regular dictionary structure and returns a jails configuration properties by defined by the user, host globals or iocage defaults.

```python
jail = iocage.Jail("myjail")
print(str(jail.config))
```

The JailConfig is compatible with all prior iocage variants, so that it is possible to manage jails with most recent JSON configuration files stored in the Jails root dataset as well as legacy formats in form of an UCL file or even ZFS properties (as used by iocage_legacy). When reading existing jails from disk, the currently used configuration storage format is automatically detected and continued to be used when saving changes. The most recent format seen on a host defines the format that iocage will use to create new jails with, which is neat when updating hosts that are maintained by an existing installation.

Note: Sceptical users may use libiocage with existing jails without modification of the format. A later migration will upgrade the whole jail to the most recent format, but besides the absence of some newer features there is no reason to migrate a jail at all.

