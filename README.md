# deekayen.repo_ius

[![CI](https://github.com/deekayen/ansible-role-ius/actions/workflows/ci.yml/badge.svg)](https://github.com/deekayen/ansible-role-ius/actions/workflows/ci.yml) [![Ansible Galaxy](https://img.shields.io/badge/galaxy-deekayen.repo__ius-blue.svg)](https://galaxy.ansible.com/ui/standalone/roles/deekayen/repo_ius/) [![Project Status: Inactive – The project has reached a stable, usable state but is no longer being actively developed; support/maintenance will be provided as time allows.](https://www.repostatus.org/badges/latest/inactive.svg)](https://www.repostatus.org/#inactive) ![MIT license](https://img.shields.io/badge/license-MIT-blue)

> **Deprecated.** IUS only ever published packages for EL 7, which reached end
> of life in June 2024. The role is kept for existing EL 7 hosts, which need
> ansible-core 2.16 or older. CI lints and syntax-checks it but no longer
> converges it on a running system.

An Ansible role that installs the release package for the [IUS](https://ius.io/) (Inline with Upstream Stable) yum repository on RHEL and CentOS 7, then refreshes the yum cache so later roles in the same play can install IUS packages.

The Galaxy name is `deekayen.repo_ius`, not `deekayen.ius`.

## Requirements

- ansible-core 2.16 or older on the controller. As of October 2026, the [Ansible support matrix](https://docs.ansible.com/ansible/latest/reference_appendices/release_and_maintenance.html) lists Python 2.7 and 3.6 as target versions for 2.16 but not 2.17, and EL 7 ships those two Pythons.
- An EL 7 target. `tasks/assert.yml` fails the play on any other OS family or major version.
- Outbound HTTPS from the target to `ius_repo_url`.
- Privilege escalation on the target. The package and cache tasks set `become: true` themselves.
- The `geerlingguy.repo-epel` role, pulled in as a dependency. It needs the `community.general` collection.

## Supported platforms

| Platform | Versions |
| --- | --- |
| EL (RHEL, CentOS) | 7 |

CI runs `ansible-lint` and `ansible-playbook --syntax-check` only.

## Installation

From Ansible Galaxy:

```bash
ansible-galaxy role install deekayen.repo_ius
ansible-galaxy collection install community.general
```

Or pin it in `requirements.yml`:

```yaml
---
roles:
  - name: deekayen.repo_ius
    src: https://github.com/deekayen/ansible-role-ius.git
    scm: git
    version: main

collections:
  - name: community.general
```

```bash
ansible-galaxy install -r requirements.yml
```

## Role variables

| Variable | Default | Description |
| --- | --- | --- |
| `ius_repo_url` | `https://repo.ius.io/` | Base URL for the IUS release RPMs. Must start with `http://` or `https://`. Point it at an internal mirror if the host can't reach `repo.ius.io`. |
| `ius_enable` | `true` | Install `ius-release-el7.rpm`. |
| `ius_enable_testing` | `false` | Install `ius-testing-7.rpm`. |
| `ius_enable_archive` | `false` | Intended to add the IUS archive repository. See [Known issues](#known-issues). |
| `proxy_url` | `""` | Optional proxy URL, for example `https://USERNAME:PASSWORD@HOST:PORT`. When set, the role sends an HTTP `CONNECT` to it and fails if the proxy does not answer. See [Known issues](#known-issues). |

## Dependencies

- [geerlingguy.repo-epel](https://github.com/geerlingguy/ansible-role-repo-epel), declared in `meta/main.yml`, so Galaxy installs it automatically.

## Example playbook

```yaml
---
- name: Add the IUS repository to EL 7 hosts.
  hosts: el7_legacy

  vars:
    ius_repo_url: https://mirror.example.internal/ius/

  roles:
    - deekayen.repo_ius
```

`mirror.example.internal` is a placeholder for an internal IUS mirror.

## Known issues

- `ius_enable_archive: true` installs `ius-testing-7.rpm`, the same package as `ius_enable_testing`, not an archive release package.
- `proxy_url` is only used for the reachability check. It is not passed to yum, so setting it does not make the package install go through the proxy. Set a `proxy=` line in `/etc/yum.conf`, or an `http_proxy` and `https_proxy` environment for the play, if the host needs a proxy.

## Development

CI runs on every push to `main` and every pull request (see `.github/workflows/ci.yml`). It installs the test dependencies from `tests/requirements.yml`, runs `ansible-lint --profile production`, and syntax-checks `tests/test.yml`. To run the same checks locally:

```bash
pip3 install ansible-lint
ansible-galaxy install -r tests/requirements.yml
ansible-lint --profile production
mkdir -p .ansible/roles && ln -sfn "$PWD" .ansible/roles/deekayen.repo_ius
ANSIBLE_ROLES_PATH=.ansible/roles:~/.ansible/roles ansible-playbook --syntax-check tests/test.yml -i tests/inventory
```

The repository also has a `.pre-commit-config.yaml`; run `pre-commit run --all-files` before pushing.

### Repository layout

| Path | Purpose |
| --- | --- |
| `tasks/main.yml` | Proxy check, release package installs, and handler flush. |
| `tasks/assert.yml` | EL 7 check and input validation, tagged `always`. |
| `handlers/main.yml` | Runs `yum makecache` directly, since current ansible-core maps `yum` to `dnf`. |
| `defaults/main.yml` | Every user-facing variable. |
| `tests/` | Syntax-check playbook, inventory, and test requirements used by CI. |

## Releases

Pushing a git tag runs `.github/workflows/release.yml`, which imports the tagged commit into Ansible Galaxy as `deekayen.repo_ius`. The import needs a `GALAXY_API_KEY` repository or organization secret.

## License

MIT. See [LICENSE](LICENSE).

## Authors

[Kirill Sevriugin](https://kisev.me) wrote the original role and later deleted its GitHub repository. [David Norman](https://github.com/deekayen) asked for a backup copy, forked it, and maintains this copy.
