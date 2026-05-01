# Ansible Role: Vaultwarden

An Ansible role that deploys Vaultwarden as a rootless Podman container managed by systemd Quadlets.

## Requirements

- The target system must have Podman installed (the [`podman`](../podman/) role is recommended for environment setup).

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable | Default Value | Description |
| --- | --- | --- |
| `vaultwarden_user` | **Required** | The system user that will run the rootless container. |
| `vaultwarden_bind_ip` | undefined | Optional, the host IP address to bind the Vaultwarden port to (don't set if you use a reverse proxy with `vaultwarden_network`). |
| `vaultwarden_bind_port` | `8080` | Optional, the host port to map to Vaultwarden's port 80 if `vaultwarden_bind_ip` is set |
| `vaultwarden_network` | undefined | Optional Podman network name for the container to join. |
| `vaultwarden_data_dir` | `"/home/{{ vaultwarden_user }}/vaultwarden/data"` | Host directory for persistent data storage. |
| `vaultwarden_domain` | **Required** | The full URL for the Vaultwarden instance (e.g., `https://vault.example.com`). |
| `vaultwarden_signups_allowed` | `false` | Whether to allow new user registrations. |
| `vaultwarden_admin_token_hash` | **Required** | The PHC Argon2id hash of your admin token. |

## Testing

This role uses Molecule and Vagrant for testing.

### Setup

Create a Python virtual environment:

```bash
python3 -m venv .venv

source .venv/bin/activate
# or for fish
source .venv/bin/activate.fish
```

Install dependencies in the virtual environment:

```bash
# From the repository root
pip install -r requirements.txt
```

### Run Tests

```bash
# From the repository root
cd roles/vaultwarden
molecule test
```

## Example Playbook

```yaml
- name: Deploy Vaultwarden
  hosts: all
  become: true
  roles:
    - role: podman
      vars:
        podman_user: vaultwarden
    - role: vaultwarden
      vars:
        vaultwarden_domain: "https://vault.example.com"
        # Generate hash, see https://github.com/dani-garcia/vaultwarden/wiki/Enabling-admin-page#secure-the-admin_token
        vaultwarden_admin_token_hash: "$argon2id$v=19$m=65540,t=3,p=4$..."
        vaultwarden_signups_allowed: false
```
