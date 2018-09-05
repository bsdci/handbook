---
title: "Getting Started"
---

In this section you'll learn how to get iocage and all its dependencies installed, as well as how to get a basic `jail` running.

The most basic prerequisite is a system with at least FreeBSD 10.
There are plans to make iocage work on [all systems supporting ZFS](milestone link), but some of our [dependencies](issues link) will first need porting.

## Dependencies

As there is currently no package or port of iocage, to get started we'll first need to install `git`

```sh
# pkg install git
```

Let's also create `/usr/local/src` for the base of our operations:

```sh
# mkdir -p /usr/local/src
# cd /usr/local/src
```

## Installation

Please clone the repository from: https://github.com/iocage/libiocage.git

```sh
~src # git clone https://github.com/iocage/libiocage.git
~src # cd libiocage
```

We can now use the project's `Makefile` to install all necessary *system* dependencies:

```sh
~libiocage # make deps
```

To install iocage, we will use the `install-dev` target, which installs additional dependencies for development, and allows us to use `/usr/local/src/libiocage` for playing with project in the Python REPL:

```sh
~libiocage # make install-dev
```

In addition to several [code and style checkers](link to code & style checkers) this also installs [sphinx tools for documentation generation](link to API docs) and GNU Make, which it uses underneath.

Let's test our setup so far:

```
~libiocage # ioc list
iocage is not activated yet - please run `ioc activate <POOL>` first and select a pool
```

As the error suggests, we'll have to configure iocage first before usage.
One way to do that is to run the suggested command.
However, we'll be using a more versatile method:

## Global Configuration

We can set our first dataset for iocage's use with `sysrc(8)` or by editing `rc.conf(5)`:

```sh
~libiocage # sysrc ioc_dataset_default="zroot/iocage"
ioc_dataset_default: zroot/iocage -> zroot/iocage
```

and verify now that it really works with `ioc list`, which should return an empty table:

```sh
~libiocage # ioc list
+-----|-----------|---------|---------|----------+
| JID | FULL_NAME | RUNNING | RELEASE | IP4_ADDR |
+=====+===========+=========+=========+==========+
+-----|-----------|---------|---------|----------+
```

## The First Jail
