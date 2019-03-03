---
title: Networking
---
Iocage is not meant to be a network manager.
It provides configuration interfaces for common network scenarios, but the general assumption is that networking is already configured on the host.

There are two modes of network operation in iocage: VNET on/off.
The `vnet` property of a jail defined which of the modes is enabled:

```sh
# global
{{site.ioc_cli_tool}} set vnet=on defaults
# per-jail
{{site.ioc_cli_tool}} set vnet=on <JAIL_NAME>
```

### VNET

Like previous iocage versions, libioc comes with support for VNET/VIMAGE.
In Freebsd 11.1-RELEASE there is no default kernel support for VIMAGE/VNET, so that this network mode requires building a custom kernel with `options VIMAGE`.

#### Example

```sh
ifconfig bridge0 create
ifconfig bridge0 10.23.42.1/24
{{site.ioc_cli_tool}} create jail-a
{{site.ioc_cli_tool}} create jail-b
{{site.ioc_cli_tool}} set vnet=on interfaces="vnet0:bridge0" ip4_addr="vnet0|10.23.42.2/24" defaultrouter="10.23.42.1" jail-a
{{site.ioc_cli_tool}} set vnet=on interfaces="vnet0:bridge0" ip4_addr="vnet0|10.23.42.3/24" defaultrouter="10.23.42.1" jail-b
{{site.ioc_cli_tool}} start jail-a,jail-b
```

#### Secure VNET

Jails that are connected to the same bridge device may steal each others IP addresses or claim to have a different Mac address by changing the interface parameters from within the jail.
This can be mitigated by inserting another bridge device in between the host bridge and the jails epair device.
IPFW is then configured to whitelist the jails IP and Mac pair in both directions on layer 2.

##### Host Configuration

```sh
sysrc firewall_type=open
sysrc firewall_enable=YES
sysctl net.link.ether.ipfw=1
sysctl net.link.bridge.ipfw=1
service ipfw start
```

##### Jail Configuration

Secure VNET can be enabled by using `::` as interface/bridge delimiter. For example:

```sh
ifconfig bridge0 create
ifconfig bridge0 10.23.42.1/24
{{site.ioc_cli_tool}} create myjail vnet=on interfaces="vnet0::bridge0" ip4_addr="vnet0|10.23.42.2/24" defaultrouter="10.23.42.1"
```

### Non-VNET (default)

Whenever a Jail is started with `vnet=off` configuration property.
Iocage forwards the `ip4_addr` and `ip6_addr` configuration properties to the [jail command](https://www.freebsd.org/cgi/man.cgi?jail), so that the network configuration is similar to VNET.
When using this network mode jails can list all existing host interfaces.

#### Example

```sh
{{site.ioc_cli_tool}} create non-vnet-jail
{{site.ioc_cli_tool}} set vnet=off ip4_addr="em0|10.1.1.10/24" non-vnet-jail
{{site.ioc_cli_tool}} start non-vnet-jail
```
