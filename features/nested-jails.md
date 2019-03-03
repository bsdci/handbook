---
title: Nested Jails
---
It is possible to use libioc within another jail instance, so that it is possible to run nested jails.
The requirements to run libioc within another iocage jail are

- At least one ZFS dataset is shared with the jail that will become the iocage root dataset
- An maximum number of allowed jails to start (`children_mac`)
- The ability to mount NullFS and ZFS

```shell-session
host # {{site.ioc_cli_tool}} create -b -n nested-host
host # zfs create -o jailed=on zroot/jailed
host # ifconfig bridge1337 create inet 10.42.0.1/24 up
host # {{site.ioc_cli_tool}} set \
    jail_zfs=yes \
    jail_zfs_dataset=zroot/jailed \
    children_max=10 \
    securelevel=0 \
    allow_chflags=yes \
    allow_mount=yes \
    allow_mount_devfs=yes \
    allow_mount_nullfs=yes \
    allow_mount_zfs=yes \
    allow_raw_sockets=yes \
    enforce_statfs=yes \
    nested-host
```

#### Mount fdescfs on the nested jail host
```shell-session
host # {{site.ioc_cli_tool}} set \
    allow_mount_fdescfs=yes \
    exec_poststart="mount -t fdescfs null /dev/fd" \
    nested-host
```

#### Optional for VNET networking for the nested jail host
```shell-session
host # {{site.ioc_cli_tool}} set \
    vnet=yes \
    interfaces="vnet0:bridge0" \
    ip4_addr="vnet0|10.42.0.99/24" \
    defaultrouter=10.42.0.1 \
    nested-host
```

Within the jail libioc can be configured to use the shared ZFS filesystem:

```shell-session
nested-host # pkg install -y git-lite
nested-host # git clone https://github.com/bsdci/libioc
nested-host # cd libioc/
nested-host # make install
nested-host # sysrc {{site.ioc_cli_tool}}_dataset_ioc="root/jailed/iocage"
nested-host # {{site.ioc_cli_tool}} fetch -r {{site.current_fbsd_release}}
nested-host # {{site.ioc_cli_tool}} create -b -n nested-jail
nested-host # ...
```
