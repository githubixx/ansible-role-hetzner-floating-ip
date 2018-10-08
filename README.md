ansible-role-hetzner-floating-ip
================================

This Ansible role adds a floating IP (v4 and/or v6) to a [Hetzner Cloud](https://www.hetzner.cloud) host. Additionally you can change the source IP to the floating IP (which may be important for mailserver).

Versions
--------

I tag every release and try to stay with [semantic versioning](http://semver.org). If you want to use the role I recommend to checkout the latest tag. The master branch is basically development while the tags mark stable releases. But in general I try to keep master in good shape too.

Requirements
------------

A Hetzner Cloud VM ;-)

Changelog
---------

**v1.0.0**

- initial implementation

Role Variables
--------------

This variables can be defined in `group_vars` or `host_vars`:

```
# Filename that contains the floating v4 IP configuration in
# "/etc/network/interfaces.d/". ".cfg" suffix will be appended
# automatically.
hetzner_floating_ipv4_filename: "99-floating-ipv4"
# By default "eth0" is the only interface that already exists.
# This is the interface name that will be created and the 
# floating IPv4 assigned.
hetzner_floating_ipv4_iface: "eth0:1"
# Filename that contains the floating v6 IP configuration in
# "/etc/network/interfaces.d/". ".cfg" suffix will be appended
# automatically.
hetzner_floating_ipv6_filename: "99-floating-ipv6"
# By default "eth0" is the only interface that already exists.
# This is the interface name that will be created and the
# floating IPv6 assigned.
hetzner_floating_ipv6_iface: "eth0:1"
```

Additionally you need to set a few variables per host in `host_vars`. E.g. to add a IPv4 floating IP and use this floating IP as source IP you can add this variable and settings:

```
hetzner_floating_ipv4_options:
  - { option: "method",  value: "static" }
  - { option: "address", value: "12.23.34.45" }
  - { option: "netmask", value: "32" }
  - { option: "post-up", value: "ip route del default; ip route add default dev eth0 via 172.31.1.1 src 12.23.34.45" }
```

If you used the defaults above this will create a file `/etc/network/interfaces.d/99-floating-ipv4.cfg` with this content:

```
auto eth0:1
iface eth0:1 inet static
    address 12.23.34.45
    netmask 32
    post-up ip route del default; ip route add default dev eth0 via 172.31.1.1 src 12.23.34.45
```

Please be aware that this role won't restart networking or add the interface automatically! Either reboot the host or restart manually. Of course you can add a Ansible handler if you like ;-) As this role uses the Ansible [interfaces_file](https://docs.ansible.com/ansible/2.4/interfaces_file_module.html) module the same restrictions apply regarding `pre-up`, `up`, `post-up` and `down` [options](https://docs.ansible.com/ansible/2.4/interfaces_file_module.html#options).

Here is a example for a IPv6 interface:

hetzner_floating_ipv6_options:
  - { option: "method",  value: "static" }
  - { option: "address", value: "2a01:4f8:1111:1111::1" }
  - { option: "netmask", value: "64" }

This will create a file `/etc/network/interfaces.d/99-floating-ipv6.cfg` with this content:

```
auto eth0:1
iface eth0:1 inet static
    address 2a01:4f8:1111:1111::1
    netmask 64
```

Example Playbook
----------------

```
- hosts: floating-ip-hosts
  roles:
    - githubixx.hetzner-floating-ip
```

License
-------

GNU GENERAL PUBLIC LICENSE Version 3

Author Information
------------------

[http://www.tauceti.blog](http://www.tauceti.blog)
