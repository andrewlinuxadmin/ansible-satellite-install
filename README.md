# satellite_install

Ansible role for automated installation and configuration
of Red Hat Satellite.

## Requirements

- RHEL 9 (x86_64) target host
- Ansible Core >= 2.14
- Python >= 3.9
- Red Hat Satellite manifest (`manifest.zip`)
- Red Hat CDN access (RHSM credentials)

## Installation

The `requirements.yml` shipped with this repository
installs both the role **and** all required collections
(including Satellite collections from their Git
repositories) in a single command:

```bash
ansible-galaxy install -r requirements.yml
```

This is equivalent to running both commands separately:

```bash
ansible-galaxy role install \
  git+https://github.com/acarlos/ansible-satellite-install.git,main
ansible-galaxy collection install -r requirements.yml
```

> **Note:** `ansible-galaxy role install` alone does
> **not** install collection dependencies. Always use
> `ansible-galaxy install -r requirements.yml` to ensure
> everything is in place.

## Role variables

### Required (must be passed via playbook vars)

| Variable | Description |
| --- | --- |
| `satellite_install_username` | Satellite admin username |
| `satellite_install_password` | Satellite admin password |
| `satellite_install_organization` | Initial organization |
| `satellite_install_location` | Initial location |
| `satellite_install_version` | Satellite version |
| `satellite_install_manifest_path` | Manifest file path |
| `satellite_install_rhel_versions` | RHEL versions list |
| `satellite_install_lifecycle_envs` | Lifecycle environments |
| `satellite_install_rhsm_username` | RHSM username |
| `satellite_install_rhsm_password` | RHSM password |

### Optional (have defaults in `defaults/main.yml`)

| Variable | Default | Description |
| --- | --- | --- |
| `satellite_install_scenario` | `"satellite"` | Scenario |
| `satellite_install_download_policy` | `"on_demand"` | Download policy |
| `satellite_install_tuning` | `"default"` | Tuning profile |
| `satellite_install_force_manifest` | `false` | Force reimport |
| `satellite_install_epel` | *(not set)* | Enable EPEL |

## Usage

A complete example playbook, inventory and configuration
are available in the
[examples/](examples/) directory.

```yaml
---
- name: Satellite Installer Playbook
  hosts: satellite

  vars_prompt:
    - name: satellite_install_rhsm_username
      prompt: "RHSM Username"
      private: false
    - name: satellite_install_rhsm_password
      prompt: "RHSM Password"
      private: true

  tasks:
    - name: Install and configure Red Hat Satellite
      ansible.builtin.include_role:
        name: satellite_install
      vars:
        satellite_install_username: "admin"
        satellite_install_password: "redhat"
        satellite_install_organization: "ACME"
        satellite_install_location: "ACME"
        satellite_install_version: "6.18"
        satellite_install_manifest_path: "manifest.zip"
        satellite_install_rhel_versions:
          - 8
          - 9
          - 10
        satellite_install_epel: true
        satellite_install_lifecycle_envs:
          - name: "DEV"
            prior: "Library"
          - name: "HML"
            prior: "DEV"
          - name: "PRD"
            prior: "HML"
```

### Run specific steps using tags

```bash
ansible-playbook satellite-install.yml --tags repos
```

### Force manifest reimport

```bash
ansible-playbook satellite-install.yml --tags manifest \
  -e satellite_install_force_manifest=true
```

## Available tags

| Tag | Description |
| --- | --- |
| `firewall` | Firewall configuration |
| `register` | RHSM registration |
| `update` | Server update |
| `install` | Satellite installation |
| `manifest` | Manifest import |
| `repos` | Repository enablement |
| `sync` | Repository synchronization |
| `sync-plan` | Sync plan configuration |
| `lifecycle` | Lifecycle environments |
| `content-view` | Content view creation |
| `publish` | Content view publishing |
| `promote` | Content view promotion |
| `ak` | Activation key creation |
| `global-parameter` | Global parameters |
| `hostgroups` | Host group configuration |

## Acknowledgments

This role was inspired by the
[Satellite Foreman Easy Installer][1] project
by [Waldirio Pinheiro (Qikfix)][2].
Thank you for the original work and inspiration!

[1]: https://github.com/Qikfix/satellite_foreman_easy-installer
[2]: https://github.com/Qikfix

## License

MIT
