---
title: Global Configuration
---
We can set our first dataset for iocage's use with `sysrc(8)` or by editing `rc.conf(5)`:

```shell-session
~libiocage # sysrc {{site.iocage_cli_tool}}_dataset_default="zroot/iocage"
{{site.iocage_cli_tool}}_dataset_default: zroot/iocage -> zroot/iocage
```

and verify now that it really works with `{{site.iocage_cli_tool}} list`, which should return an empty table:

```shell-session
# {{site.iocage_cli_tool}} list
+-----|-----------|---------|---------|----------+
| JID | FULL_NAME | RUNNING | RELEASE | IP4_ADDR |
+=====+===========+=========+=========+==========+
+-----|-----------|---------|---------|----------+
```