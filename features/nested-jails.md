It is possible to use libiocage within another jail instance, so that it is possible to run nested jails. The requirements to run libiocage within another iocage jail are:

- At least one ZFS dataset is shared with the jail that will become the iocage root dataset
- An maximum number of allowed jails to start (`children_mac`)
- The ability to mount NullFS and ZFS

```
host # ioc create -b -n nested-host
host # zfs create -o jailed=on zroot/jailed
host # ifconfig bridge1337 create inet 10.42.0.1/24 up
host # ioc set \
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
    vnet=yes \
    interfaces="vnet0:bridge0" \
    ip4_addr="vnet0|10.42.0.99/24" \
    defaultrouter=10.42.0.1 \
    nested-host
```

Within the jail libiocage can be configured to use the shared ZFS filesystem:

```
nested-host # pkg install -y git-lite
nested-host # git clone https://github.com/iocage/libiocage
nested-host # cd libiocage/
nested-host # make install
nested-host # sysrc ioc_dataset_ioc="root/jailed/iocage"
nested-host # ioc fetch -r 11.1-RELEASE
nested-host # ioc create -b -n nested-jail
nested-host # ...
```