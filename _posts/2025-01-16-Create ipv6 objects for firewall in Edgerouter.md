---
title: Create ipv6 objects for firewall in Edgerouter
date: 2025-01-16 10:20:00 +/-TTTT
categories: [Ubiquiti]
tags: [Edgerouter, ipv6]     # TAG names should always be lowercase
---

After enabling IPv6 on my Edgerouter, I found that configuring firewall rules can be challenging, as the system does not support the use of interfaces within the firewall rules. Instead, one must specify target or source IP addresses or ranges.

Given that Edgerouter operates on Linux and provides access to the CLI, I began experimenting with reading the IPv6 information from the network interface and creating objects using a script. This i what I have come up with so far.

Create a script file (for example: vi /config/scripts/create_ipv6_objects.sh) with the following content.

```bash
#!/bin/vbash

#1. Read global ipv6 and replace ::1/64 with ::/64 to remove the host-part. All my internal networks should match this pattern.
switch0_ipv6=$(ip -6 addr show dev switch0 | grep "global" | awk '/inet6/ {print $2}' | sed 's/::1\/64/::\/64/g')

#2. Start editing the config
/opt/vyatta/sbin/vyatta-cfg-cmd-wrapper begin

#3. Create objects if variable contains value
if [ -n "$switch0_ipv6" ]; then
        /opt/vyatta/sbin/vyatta-cfg-cmd-wrapper set firewall group ipv6-network-group Net-LAN-v6 description ''
        /opt/vyatta/sbin/vyatta-cfg-cmd-wrapper set firewall group ipv6-network-group Net-LAN-v6 ipv6-network "$switch0_ipv6"
fi

#4. Commit and save
/opt/vyatta/sbin/vyatta-cfg-cmd-wrapper commit
/opt/vyatta/sbin/vyatta-cfg-cmd-wrapper save
```

If you have more interfaces that you want objects for then repeate steps 1 and 3.

Then add the script as a scheduled task with desired interval
```bash
configure
set system task-scheduler task create_ipv6_objetcs executable path /config/scripts/create_ipv6_objects.sh
set system task-scheduler task  create_ipv6_objetcs interval 1h
commit ; save ; exit
```
