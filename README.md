ansible-role-hetzner-floating-ip
================================

This Ansible role adds a floating IP (v4 and/or v6) to a [Hetzner Cloud](https://www.hetzner.cloud) host. Additionally you can change the source IP to the floating IP.

Versions
--------

I tag every release and try to stay with [semantic versioning](http://semver.org). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too.

Requirements
------------

A [Hetzner Cloud VM](https://www.hetzner.cloud) and a [floating IP](https://docs.hetzner.com/cloud/floating-ips/persistent-configuration/)

Changelog
---------

see [CHANGELOG.md](https://github.com/githubixx/ansible-role-hetzner-floating-ip/blob/master/CHANGELOG.md)

Role Variables
--------------

This variables can be defined in `group_vars` or `host_vars`:

```
# Filename that contains the floating v4 IP configuration in
# "/etc/network/interfaces.d/". ".cfg" suffix will be appended
# automatically.
hetzner_floating_ipv4_filename: "99-floating-ipv4"

# Filename that contains the floating v6 IP configuration in
# "/etc/network/interfaces.d/". ".cfg" suffix will be appended
# automatically.
hetzner_floating_ipv6_filename: "99-floating-ipv6"
```

Just adding the variables above isn't enough. If you just define this variables nothing will be deployed. Additionally you need to set a few variables per host in `host_vars`. E.g. to add a IPv4 floating IP and use this floating IP as source IP you can add this variable and settings:

```
hetzner_floating_ipv4_options:
  - { option: "method",  value: "static" }
  - { option: "address", value: "12.23.34.45" }
  - { option: "netmask", value: "32" }
  - { option: "post-up", value: "ip route del default; ip route add default dev eth0 via 172.31.1.1 src 12.23.34.45" }
```

Using the examples above a file called `/etc/netplan/99-floating-ipv4.yaml` will be created with this content:

```
network:
  version: 2
  ethernets:
    eth0:
      addresses:
      - 12.23.34.45/32
```

`{ option: "method",  value: "static" }` has no effect in this case. And the **post-up** hook will be located in `/etc/network/if-post-up.d/99-floating-ipv4`:

```
#!/bin/sh

ip route del default; ip route add default dev eth0 via 172.31.1.1 src 12.23.34.45
```

Here is a example for a IPv6 interface:

```
hetzner_floating_ipv6_options:
  - { option: "method",  value: "static" }
  - { option: "address", value: "2a01:4f8:1111:1111::1" }
  - { option: "netmask", value: "64" }
```

The resulting files will be located at `/etc/netplan/99-floating-ipv6.yaml` and `/etc/network/if-post-up.d/99-floating-ipv6`.

Example Playbook
----------------

```
- hosts: floating-ip-hosts
  roles:
    - githubixx.hetzner_floating_ip
```

License
-------

GNU GENERAL PUBLIC LICENSE Version 3

Author Information
------------------

[http://www.tauceti.blog](http://www.tauceti.blog)
