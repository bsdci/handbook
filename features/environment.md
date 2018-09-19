---
title: "Hook Environment Variables"
---
During startup and teardown several hook commands are executed on the host and within the jail.
Those commands are supplied environment variables that represent all Jail Config properties.

All such environment variables are written in uppercase and prefixed with `IOCAGE_`.

Environment variables are available in the following hooks:

- exec_prestart
- exec_start
- exec_created
- exec_poststart
- exec_prestop
- exec_stop
- exec_poststop

Note: The `exec_start` and `exec_stop` hooks are executed withing a jail itself.