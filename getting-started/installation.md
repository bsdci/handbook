---
title: Installation
---

Please clone the repository from: https://github.com/bsdci/libioc.git

```shell-session
~src # git clone https://github.com/bsdci/libioc.git
~src # cd libioc
```

We can now use the project's `Makefile` to install all necessary *system* dependencies:

```shell-session
~libioc # make deps
```

To install iocage, we will use the `install-dev` target, which installs additional dependencies for development, and allows us to use `/usr/local/src/libioc` for playing with project in the Python REPL:

```shell-session
~libioc # make install-dev
```

In addition to several [code and style checkers](link to code & style checkers) this also installs [sphinx tools for documentation generation](link to API docs) and GNU Make, which it uses underneath.

Let's test our setup so far:

```shell-session
~libioc # {{site.ioc_cli_tool}} list
iocage is not activated yet - please run `{{site.ioc_cli_tool}} activate <POOL>` first and select a pool
```

As the error suggests, we'll have to configure iocage first before usage.
One way to do that is to run the suggested command.
