<!--
title: "ISC DHCPd monitoring with Netdata"
custom_edit_url: https://github.com/netdata/go.d.plugin/edit/master/modules/isc_dhcpd/README.md
sidebar_label: "ISC DHCPd"
-->

# ISC DHCPd monitoring with Netdata

[`ISC DHCP`](https://www.isc.org/dhcp/) is a DHCP server that supports both IPv4 and IPv6, and is suitable for use in high-volume and high-reliability applications. 

This module monitors active leases and IP pools utilization collecting data from
the DHCP client lease database (`dhcpd.leases`).

## Requirements

`dhcpd.leases` must be readable by `netdata` user.

## Charts

-   Active Leases Total in `leases`
-   Pool Active Leases in `leases`
-   Pool Utilization in `percentage`  

## Configuration

Edit the `go.d/isc_dhcpd.conf` configuration file using `edit-config` from the Agent's [config
directory](/docs/step-by-step/step-04.md#find-your-netdataconf-file), which is typically at `/etc/netdata`.

```bash
cd /etc/netdata # Replace this path with your Netdata config directory
sudo ./edit-config go.d/isc_dhcpd.conf
```

Needs `leases_path` (path to the DHCP client lease database), and a list of IP pools to monitor.
IP pool `networks` is a space separated list of [IP ranges](https://github.com/netdata/go.d.plugin/tree/master/pkg/iprange#supported-formats).

Here is a configuration example:

```yaml
jobs:
 - name: ipv4_example
   leases_path: '/path/to/ipv4_leases_file'
   pools:
      - name: office
        networks: '192.0.2.1-192.0.2.254'
      - name: wifi 
        networks: '198.51.100.0/24'
      - name: dmz 
        networks: '203.0.113.0/255.255.255.0'

 - name: ipv6_example
   leases_path: '/path/to/ipv6_leases_file'
   pools:
      - name: office
        networks: '2001:0DB8::/64'
      - name: wifi 
        networks: '2001:0DB8:0:1::/64'
      - name: dmz 
        networks: '2001:0DB8:0:2::/64'
```

For all available options, see the ISC dhcpd collector's [configuration
file](https://github.com/netdata/go.d.plugin/blob/master/config/go.d/isc_dhcpd.conf).

## Troubleshooting

To troubleshoot issues with the ISC dhcpd collector, run the `go.d.plugin` with the debug option enabled.
The output should give you clues as to why the collector isn't working.

First, navigate to your plugins directory, usually at `/usr/libexec/netdata/plugins.d/`. If that's not the case on your
system, open `netdata.conf` and look for the setting `plugins directory`. Once you're in the plugin's directory, switch
to the `netdata` user.

```bash
cd /usr/libexec/netdata/plugins.d/
sudo -u netdata -s
```

You can now run the `go.d.plugin` orchestrator to debug the collector:

```bash
./go.d.plugin -d -m isc_dhcpd
```