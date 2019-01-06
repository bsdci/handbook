---
title: Global Configuration
---
We can set our first dataset for iocage's use with `sysrc(8)` or by editing `rc.conf(5)`:

```shell-session
~libiocage # sysrc {{site.ioc_cli_tool}}_dataset_default="zroot/iocage"
{{site.ioc_cli_tool}}_dataset_default: zroot/iocage -> zroot/iocage
```

and verify now that it really works with `{{site.ioc_cli_tool}} list`, which should return an empty table:

```shell-session
# {{site.ioc_cli_tool}} list
+-----|-----------|---------|---------|----------+
| JID | FULL_NAME | RUNNING | RELEASE | IP4_ADDR |
+=====+===========+=========+=========+==========+
+-----|-----------|---------|---------|----------+
```

If you're noticing a bit of a delay in `{{site.ioc_cli_tool}} list`, this might be due to a well-known [performance issue](https://github.com/iocage/libiocage/issues/514).
It occurs especially on large machines, and we recommend that you mount [`fdescfs(5)`](https://www.freebsd.org/cgi/man.cgi?fdescfs):

```sh
mount -t fdescfs null /dev/fd
```

and persist it to `/etc/fstab`:

```
fdescfs /dev/fd fdescfs rw	0 0
```
