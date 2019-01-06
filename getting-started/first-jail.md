---
title: The First Jail
---
To create our first Jail, we first have to download a [Release](../library-essentials/#release).
We can fetch the host system's default release by pressing enter on the `{{site.ioc_cli_tool}} fetch` prompt:

```shell-session
# {{site.ioc_cli_tool}} fetch
[0] 10.1-RELEASE (EOL)
[1] 10.2-RELEASE (EOL)
[2] 10.3-RELEASE (EOL)
[3] 10.4-RELEASE
[4] 11.0-RELEASE (EOL)
...etc...

Type the number of the desired RELEASE
Press [Enter] to fetch the default selection ({{site.current_fbsd_release}}) [6]:
```

We can verify that the release was downloaded successfully with

```shell-session
# {{site.ioc_cli_tool}} list --release
+--------------+
|  FULL_NAME   |
+==============+
| {{site.current_fbsd_release}} |
+--------------+
```

Now, let's create our Jail and verify that everything works as expected:

```shell-session
# {{site.ioc_cli_tool}} create hello-world
hello-world successfully created from {{site.current_fbsd_release}}!
# {{site.ioc_cli_tool}} list
+-----|-------------|---------|--------------|----------+
| JID |  FULL_NAME  | RUNNING |   RELEASE    | IP4_ADDR |
+=====+=============+=========+==============+==========+
| -   | hello-world | no      | {{site.current_fbsd_release}} |          |
+-----|-------------|---------|--------------|----------+
```

And we're ready to start:

```shell-session
# {{site.ioc_cli_tool}} start hello-world
[+] JailResolverConfig: OK [0.003s]
[+] JailLaunch@hello-world: OK [0.496s]
hello-world running as JID 3
```

Again, with `{{site.ioc_cli_tool}} list` we can see what's up:

```shell-session
+-----|-------------|---------|--------------|----------+
| JID |  FULL_NAME  | RUNNING |   RELEASE    | IP4_ADDR |
+=====+=============+=========+==============+==========+
| 3   | hello-world | yes     | {{site.current_fbsd_release}} |          |
+-----|-------------|---------|--------------|----------+
```

And we look at the system's `ps(1)` to see it, indeed running as `jail` with the `jid` 3:

```shell-session
# ps -aux -J 3
USER  PID %CPU %MEM  VSZ  RSS TT  STAT STARTED    TIME COMMAND
root 3800  0.0  0.1 6412 2376  -  IsJ  20:53   0:00.00 /usr/sbin/syslogd -ss
root 3854  0.0  0.1 6464 2384  -  IsJ  20:53   0:00.01 /usr/sbin/cron -s
```

Now, if you compare this to the output of `ps` under `{{site.ioc_cli_tool}} exec`, it should look similar:

```shell-session
# {{site.ioc_cli_tool}} exec hello-world -- ps -aux
USER  PID %CPU %MEM  VSZ  RSS TT  STAT STARTED    TIME COMMAND
root 3800  0.0  0.1 6412 2376  -  IsJ  20:53   0:00.00 /usr/sbin/syslogd -ss
root 3854  0.0  0.1 6464 2384  -  IsJ  20:53   0:00.01 /usr/sbin/cron -s
root 4016  0.0  0.1 6992 2840  3  R+J  21:13   0:00.00 ps -aux
```

except now `ps(1)` also lists itself.
The hello-world Jail believes to be alone in the world, in fact, it believes to be Jail 0:

```shell-session
# {{site.ioc_cli_tool}} exec hello-world -- ps -aux -J 0
USER  PID %CPU %MEM  VSZ  RSS TT  STAT STARTED    TIME COMMAND
root 3800  0.0  0.1 6412 2376  -  SsJ  20:53   0:00.00 /usr/sbin/syslogd -ss
root 3854  0.0  0.1 6464 2384  -  SsJ  20:53   0:00.01 /usr/sbin/cron -s
root 4029  0.0  0.1 6992 2844  3  R+J  21:15   0:00.00 ps -aux -J 0
```