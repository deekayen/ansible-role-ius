# ansible-role-ius

[![CI](https://github.com/deekayen/ansible-role-ius/workflows/CI/badge.svg)](https://github.com/deekayen/ansible-role-ius/actions?query=workflow%3ACI) [![Project Status: Inactive – The project has reached a stable, usable state but is no longer being actively developed; support/maintenance will be provided as time allows.](https://www.repostatus.org/badges/latest/inactive.svg)](https://www.repostatus.org/#inactive)

> **Deprecated.** IUS only ever published packages for EL 7, which reached end
> of life in June 2024. The role is kept for existing EL 7 hosts, which need
> ansible-core 2.16 or older. CI lints and syntax-checks it but no longer
> converges it on a running system.

Install the [IUS repository](https://ius.io/) (Inline with Upstream Stable) for RHEL/CentOS.
Allow install on hosts without direct internet connection (used http(s) proxy).

## Requirements

RHEL/CentOS 7 version

## Role Variables

See defaults/main.yml for details

```yaml
proxy_url: ""
ius_repo_url: "https://repo.ius.io/"
ius_enable: true
ius_enable_testing: false
ius_enable_archive: false
```

## Dependencies

Add next roles to your playbook:

- [geerlingguy.repo-epel](https://github.com/geerlingguy/ansible-role-repo-epel)

## Example Playbook

### Ansible Galaxy

```bash
$ cat requirements.yml
- name: deekayen.repo_ius

$ ansible-galaxy install -r requirements.yml
```

### From fork or this repo

```bash
$ cat requirements.yml
- src: git@github.com:deekayen/ansible-role-ius.git
  version: main

$ ansible-galaxy install -r requirements.yml
```

```yaml
- hosts: servers
  roles:
    - deekayen.repo_ius
```

## License

MIT

## Author Information

[Kirill Sevriugin](https://kisev.me) <kisevme@gmail.com> was the original author but deleted the GitHub repository. David Norman asked for a backup copy and then forked it.
