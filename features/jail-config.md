Every Jail in iocage has a `config` property that points to its JailConfg instance. This object behaves like a regular dictionary structure and returns a jails configuration properties by defined by the user, host globals or iocage defaults.

```python
jail = iocage.Jail("myjail")
print(str(jail.config))
```

The JailConfig is compatible with all prior iocage variants, so that it is possible to manage jails with most recent JSON configuration files stored in the Jails root dataset as well as legacy formats in form of an UCL file or even ZFS properties (as used by iocage_legacy). When reading existing jails from disk, the currently used configuration storage format is automatically detected and continued to be used when saving changes. The most recent format seen on a host defines the format that iocage will use to create new jails with, which is neat when updating hosts that are maintained by an existing installation.

Note: Sceptical users may use libiocage with existing jails without modification of the format. A later migration will upgrade the whole jail to the most recent format, but besides the absence of some newer features there is no reason to migrate a jail at all.

### Configuration Properties

| Property              | Default                       | Description   |
|-----------------------|-------------------------------|---------------|
| id                    | None                          | Unique jail identifier. (In older versions of iocage this was a random UUID.) |
| release               | None                          | Name of a jails release that may include a specific patchlevel, e.g. 11.1-RELEASE-p1 on FreeBSD |
| boot                  | False                         | Starts the jail from the iocage rc.d script during host start.          |
| priority              | 0                             | Defines the boot order. Higher numbers are started first.          |
| legacy                | False                         | Read-only value defining whether a Jail is formatted with a legacy format (UCL or ZFS Properties) |
| basejail              | False                         | True if a Jail is a Basejail.          |
| basejail_type         | "nullfs"                      | Defined whether to use NullFS mounts or ZFS clones on Jail start. (Accepted Values: `nullfs` or `zfs`) |
| clonejail             | False                         | TODO          |
| defaultrouter         | None                          | IPv4 address of the default router. May include a specific interface name to establish a point-to-point route. (e.g. `<IPv4Address>[@<Interface>]`) |
| defaultrouter6        | None                          | IPv6 address of the default router. May include a specific interface name to establish a point-to-point route. (e.g. `<IPv6Address>[@<Interface>]`) |
| mac_prefix            | "02ff60"                      | Automatically generated MAC addresses for VNET interfaces use this prefix. |
| vnet                  | False                         | Enables VNET/VIMAGE networking. |
| interfaces            | []                            | Defines the relation between jail VNET and host interfaces. A double colon between the jail interface and the host bridge enables the Secure VNET. |
| ip4                   | "new"                         | TODO          |
| ip4_saddrsel          | 1                             | TODO          |
| ip4_addr              | None                          | A boolean option to change the formerly mentioned behaviour and disable IPv4 source address selection for the jail in favour of the primary IPv4 address of the jail. Source address selection is enabled by default for all jails and the ip4.nosaddrsel set- ting of a parent jail is not inherited for any child jails. |
| ip6                   | "new"                         | TODO          |
| ip6_saddrsel          | 1                             | TODO          |
| ip6_addr              | None                          | TODO          |
| resolver              | "/etc/resolv.conf"            | TODO          |
| host_hostuuid         | None                          | TODO          |
| host_hostname         | None                          | TODO          |
| host_domainname       | None                          | TODO          |
| devfs_ruleset         | 4                             | TODO          |
| enforce_statfs        | 2                             | TODO          |
| children_max          | 0                             | TODO          |
| allow_set_hostname    | 1                             | TODO          |
| allow_sysvipc         | 0                             | TODO          |
| allow_raw_sockets     | 0                             | TODO          |
| allow_chflags         | 0                             | TODO          |
| allow_mount           | 0                             | TODO          |
| allow_mount_devfs     | 0                             | TODO          |
| allow_mount_nullfs    | 0                             | TODO          |
| allow_mount_procfs    | 0                             | TODO          |
| allow_mount_fdescfs   | 0                             | TODO          |
| allow_mount_zfs       | 0                             | TODO          |
| allow_mount_tmpfs     | 0                             | TODO          |
| allow_quotas          | 0                             | TODO          |
| allow_socket_af       | 0                             | TODO          |
| rlimits               | None                          | TODO          |
| sysvmsg               | "new"                         | TODO          |
| sysvsem               | "new"                         | TODO          |
| sysvshm               | "new"                         | TODO          |
| exec_clean            | 1                             | TODO          |
| exec_fib              | 1                             | TODO          |
| exec_prestart         | None                          | TODO          |
| exec_created          | None                          | TODO          |
| exec_start            | "/bin/sh /etc/rc"             | TODO          |
| exec_poststart        | None                          | TODO          |
| exec_prestop          | None                          | TODO          |
| exec_stop             | "/bin/sh /etc/rc.shutdown"    | TODO          |
| exec_poststop         | None                          | TODO          |
| exec_jail_user        | "root"                        | TODO          |
| exec_timeout          | "60"                          | TODO          |
| stop_timeout          | "30"                          | TODO          |
| mount_procfs          | "0"                           | TODO          |
| mount_devfs           | "1"                           | TODO          |
| mount_fdescfs         | "0"                           | TODO          |
| securelevel           | "2"                           | TODO          |
| tags                  | []                            | TODO          |
| template              | False                         | TODO          |
| jail_zfs              | False                         | TODO          |
| jail_zfs_dataset      | None                          | TODO          |
| provisioning.method   | None                          | TODO          |
| provisioning.source   | None                          | TODO          |
| provisioning.rev      | "master"                      | TODO          |


Removal:
- vnet_interfaces
- 