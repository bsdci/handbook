---
title: "Getting Started"
---

In this section you'll learn how to get iocage and all its dependencies installed, as well as how to get a basic `jail` running.

The most basic prerequisite is a system with at least FreeBSD 10.
There are plans to make iocage work on [all systems supporting ZFS](milestone link), but some of our [dependencies](issues link) will first need porting.

## Dependencies

As there is currently no package or port of iocage, to get started we'll first need to install `git`

```shell-session
# pkg install git
```

Let's also create `/usr/local/src` for the base of our operations:

```shell-session
# mkdir -p /usr/local/src
# cd /usr/local/src
```

## Installation

Please clone the repository from: https://github.com/iocage/libiocage.git

```shell-session
~src # git clone https://github.com/iocage/libiocage.git
~src # cd libiocage
```

We can now use the project's `Makefile` to install all necessary *system* dependencies:

```shell-session
~libiocage # make deps
```

To install iocage, we will use the `install-dev` target, which installs additional dependencies for development, and allows us to use `/usr/local/src/libiocage` for playing with project in the Python REPL:

```shell-session
~libiocage # make install-dev
```

In addition to several [code and style checkers](link to code & style checkers) this also installs [sphinx tools for documentation generation](link to API docs) and GNU Make, which it uses underneath.

Let's test our setup so far:

```shell-session
~libiocage # {{site.iocage_cli_tool}} list
iocage is not activated yet - please run `{{site.iocage_cli_tool}} activate <POOL>` first and select a pool
```

As the error suggests, we'll have to configure iocage first before usage.
One way to do that is to run the suggested command.
However, we'll be using a more versatile method:

## Global Configuration

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

## The First Jail

To create our first Jail, we first have to download a [Release](../library-essentials/#release).
We can fetch the host system's default release by pressing enter on the `{{site.iocage_cli_tool}} fetch` prompt:

```shell-session
# {{site.iocage_cli_tool}} fetch
[0] 10.1-RELEASE (EOL)
[1] 10.2-RELEASE (EOL)
[2] 10.3-RELEASE (EOL)
[3] 10.4-RELEASE
[4] 11.0-RELEASE (EOL)
[5] 11.1-RELEASE
[6] 11.2-RELEASE
[7] 9.3-RELEASE (EOL)

Type the number of the desired RELEASE
Press [Enter] to fetch the default selection (11.2-RELEASE) [6]:
```

We can verify that the release was downloaded successfully with

```shell-session
# {{site.iocage_cli_tool}} list --release
+--------------+
|  FULL_NAME   |
+==============+
| 11.2-RELEASE |
+--------------+
```

Now, let's create our Jail and verify that everything works as expected:

```shell-session
# {{site.iocage_cli_tool}} create hello-world
hello-world successfully created from 11.2-RELEASE!
# {{site.iocage_cli_tool}} list
+-----|-------------|---------|--------------|----------+
| JID |  FULL_NAME  | RUNNING |   RELEASE    | IP4_ADDR |
+=====+=============+=========+==============+==========+
| -   | hello-world | no      | 11.2-RELEASE |          |
+-----|-------------|---------|--------------|----------+
```

And we're ready to start:

```shell-session
# {{site.iocage_cli_tool}} start hello-world
[+] JailResolverConfig: OK [0.003s]
[+] JailLaunch@hello-world: OK [0.496s]
hello-world running as JID 3
```

Again, with `{{site.iocage_cli_tool}} list` we can see what's up:

```shell-session
+-----|-------------|---------|--------------|----------+
| JID |  FULL_NAME  | RUNNING |   RELEASE    | IP4_ADDR |
+=====+=============+=========+==============+==========+
| 3   | hello-world | yes     | 11.2-RELEASE |          |
+-----|-------------|---------|--------------|----------+
```

And we look at the system's `ps(1)` to see it, indeed running as `jail` with the `jid` 3:

```shell-session
# ps -aux -J 3
USER  PID %CPU %MEM  VSZ  RSS TT  STAT STARTED    TIME COMMAND
root 3800  0.0  0.1 6412 2376  -  IsJ  20:53   0:00.00 /usr/sbin/syslogd -ss
root 3854  0.0  0.1 6464 2384  -  IsJ  20:53   0:00.01 /usr/sbin/cron -s
```

Now, if you compare this to the output of `ps` under `{{site.iocage_cli_tool}} exec`, it should look similar:

```shell-session
# {{site.iocage_cli_tool}} exec hello-world -- ps -aux
USER  PID %CPU %MEM  VSZ  RSS TT  STAT STARTED    TIME COMMAND
root 3800  0.0  0.1 6412 2376  -  IsJ  20:53   0:00.00 /usr/sbin/syslogd -ss
root 3854  0.0  0.1 6464 2384  -  IsJ  20:53   0:00.01 /usr/sbin/cron -s
root 4016  0.0  0.1 6992 2840  3  R+J  21:13   0:00.00 ps -aux
```

except now `ps(1)` also lists itself.
The hello-world Jail believes to be alone in the world, in fact, it believes to be Jail 0:

```shell-session
# {{site.iocage_cli_tool}} exec hello-world -- ps -aux -J 0
USER  PID %CPU %MEM  VSZ  RSS TT  STAT STARTED    TIME COMMAND
root 3800  0.0  0.1 6412 2376  -  SsJ  20:53   0:00.00 /usr/sbin/syslogd -ss
root 3854  0.0  0.1 6464 2384  -  SsJ  20:53   0:00.01 /usr/sbin/cron -s
root 4029  0.0  0.1 6992 2844  3  R+J  21:15   0:00.00 ps -aux -J 0
```

## Conclusion

We've successfully installed iocage and verified that it works.
Next we'll deepen our understanding of each of the concepts we've brushed on in this section.
