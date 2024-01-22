# Changelog

## 4.0.0

- **BREAKING**: remove support for Ubuntu <= 18.04
- **BREAKING**: remove support for `ifupdown` managed interfaces (so now `netplan.io` only)
- **BREAKING**: `meta/main.yml`: rename role to `hetzner_floating_ip` / add `role_name: hetzner_floating_ip` / add `namespace: githubixx`
- add support for Ubuntu 22.04
- add Github Actions workflow
- min. Ansible version is now `2.9`
- fix various `ansible-lint` issues
- rename `python-apt` package to `python-apt-common`

## 3.0.0

- added support for Ubuntu 20.04 LTS (Focal Fossa)
- added support for netplan.io (which is default in Ubuntu 20.04 LTS)

## no change

- deleted tag `v1.0.0` which is not compatible with Ansible Galaxy

## 2.0.0

- use correct semantic versioning as described in [semver](https://semver.org). Needed for Ansible Galaxy importer as it now insists on using semantic versioning.
- moved changelog entries to separate file
- make Ansible linter happy
- no major changes but decided to start a new major release as versioning scheme changed quite heavily

## v1.0.0

- initial implementation
