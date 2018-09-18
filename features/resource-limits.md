To limit jails or jail process resources the host needs to have the `kern.racct` tunable enabled:

```sh
[ `/sbin/sysctl -qn kern.racct.enable` -lt 1 ] && \
    echo "kern.racct.enable=1" >> /boot/loader.conf && \
    reboot
```

Resource limits affecting the whole jail are compatible with other variants of iocage, but can also be set to constrain individual processes within the jail.

### Available Resource Limits

Resource limits can be configured according to the `rctl` man page.

As input format the iocage legacy syntax may be used.
The preferred format for resource limit properties is:

```
[<action>=]<amount>[/<per>]
```

### Example

A jails memory consumption can be limited to not exceed 128M by setting the `vmemoryuse` configuration property:

```sh
{{site.iocage_cli_tool}} create -b -n limited-jail vmemoryuse=deny=128M/jail
```

The same resource limit can be set at any later time by using the `{{site.iocage_cli_tool}} set` command:

```sh
{{site.iocage_cli_tool}} set vmemoryuse=128M limited-jail
```
