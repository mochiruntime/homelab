# Ansible Role: Podman User

An Ansible role that manages rootless Podman users on Ubuntu. It ensures users exist, enables systemd lingering for rootless operation, and creates the necessary Quadlet configuration directories.

## Requirements

- `podman` should be installed on the target system. The [podman role](../podman/) can be used to install it.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable           | Default Value | Description                                                 |
| ------------------ | ------------- | ----------------------------------------------------------- |
| `podman_user_list` | `[]`          | A list of usernames to configure for rootless Podman usage. |

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
cd roles/podman_user
molecule test
```

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: podman_user
      vars:
        podman_user_list:
          - myuser
```
