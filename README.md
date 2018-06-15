# ansible-role-ius

[![Build Status](https://travis-ci.org/kisev/ansible-role-ius.svg?branch=master)](https://travis-ci.org/kisev/ansible-role-ius)

Install the [IUS repository](https://ius.io/) (Inline with Upstream Stable) for RHEL/CentOS.
Allow install on hosts without direct internet connection (used http(s) proxy).

## Requirements

RHEL/CentOS 7 version

## Role Variables

See defaults/main.yml for details

```yaml
proxy_url: ""
ius_repo_files_path: "/etc/yum.repos.d"
ius_repo_url: "https://dl.iuscommunity.org/pub/ius"
ius_repo_mirror_name: "{% if ansible_distribution == 'RedHat' %}ius-el{% else %}ius-centos{% endif %}"
ius_enable_mirrors: false
ius_enable: true
ius_enable_debuginfo: false
ius_enable_source: false
ius_enable_testing: false
ius_enable_testing_debuginfo: false
ius_enable_testing_source: false
ius_enable_dev: false
ius_enable_dev_debuginfo: false
ius_enable_dev_source: false
ius_enable_archive: false
ius_enable_archive_debuginfo: false
ius_enable_archive_source: false
```

## Dependencies

Add next roles to your playbook:

- [ansible-role-epel](https://github.com/kisev/ansible-role-epel)

## Example Playbook

### Ansible Galaxy

```bash
$ cat requirements.yml
- src: kisev/ius
  version: master

$ ansible-galaxy install -r requirements.yml
```

### From fork or this repo

```bash
$ cat requirements.yml
- src: git@github.com:kisev/ansible-role-ius.git
  scm: git
  version: master

$ ansible-galaxy install -r requirements.yml
```

```yaml
- hosts: servers
  roles:
    - kisev.ius
    # or
    - ansible-role-ius
```

## License

MIT

## Author Information

[Kirill Sevriugin](https://kisev.me) <kisevme@gmail.com>