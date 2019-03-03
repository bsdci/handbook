---
title: Errors
---
There are two kinds of errors in iocage - well-known errors that inherit from `ioc.errors.IocageException` that will not print a stack trace, but log reasonable hints and error messages, and other errors that were caused by defects in iocage itself.
The latter will raise regular a Python `Exception` that causes a stack trace.
Whenever users face a non-IocageException, [reporting an Issue on GitHub](https://github.com/bsdci/libioc/issues/new) is the right action to do.

This section focuses on the IocageException that always relates well known failure, as for example invalid use of configuration properties.
Internally iocage passes the logger instance to any such exception, so that it triggers a log entry with `error` level.
By default the command line tool `{{site.ioc_cli_tool}}` prints errors and warnings to STDOUT, so that the CLI tool is advised to `exit(1)` after spotting such occasions.