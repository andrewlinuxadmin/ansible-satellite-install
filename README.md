# Ansible Satellite Install

Playbook for automated installation and configuration of Red Hat Satellite 6.18.

## Prerequisites

- RHEL 9 (x86_64)
- Ansible Core 2.14+
- Red Hat Satellite manifest (`manifest.zip`) placed inside the `files/` directory
- Red Hat CDN access (RHSM credentials)

## Structure

```
├── ansible.cfg
├── files/
│   └── manifest.zip       # Satellite manifest (required)
├── inventory.ini
├── requirements.yml
├── satellite.yml
└── README.md
```

## Installing collections

```bash
ansible-galaxy collection install -r requirements.yml
```

## Main variables

| Variable | Default | Description |
|---|---|---|
| `sat_user` | `admin` | Satellite admin username |
| `sat_pass` | `redhat` | Admin password |
| `sat_org` | `ACME` | Initial organization |
| `sat_location` | `ACME` | Initial location |
| `sat_version` | `6.18` | Satellite version |
| `rhel_versions` | `[8, 9]` | RHEL versions for repos/CVs setup |
| `epel` | `true` | Enable EPEL repositories |
| `download_policy` | `on_demand` | Repository download policy |
| `tuning` | `default` | Satellite tuning profile |
| `manifest_path` | `manifest.zip` | Manifest file path |
| `force_manifest_import` | `false` | Force manifest reimport |
| `lifecycle_environments` | DEV/HML/PRD | List of lifecycle environments |

## Usage

```bash
# Full installation
ansible-playbook satellite.yml

# Run specific steps using tags
ansible-playbook satellite.yml --tags repos

# Force manifest reimport
ansible-playbook satellite.yml --tags manifest -e force_manifest_import=true
```

## Available tags

| Tag | Description |
|---|---|
| `firewall` | Firewall configuration |
| `register` | RHSM registration and repo enablement |
| `update` | Server update |
| `install` | Satellite installation |
| `manifest` | Manifest import |
| `repos` | Repository enablement (RHEL + EPEL) |
| `sync` | Repository synchronization |
| `sync-plan` | Sync plan configuration |
| `lifecycle` | Lifecycle environment configuration |
| `content-view` | Content view creation |
| `publish` | Content view publishing |
| `promote` | Content view promotion |
| `ak` | Activation key creation |
| `global-parameter` | Global parameter configuration |
| `hostgroups` | Host group configuration |

## Acknowledgments

This playbook was inspired by the [Satellite Foreman Easy Installer](https://github.com/Qikfix/satellite_foreman_easy-installer) project by [Waldirio Pinheiro (Qikfix)](https://github.com/Qikfix). Thank you for the original work and inspiration!
