# Ansible Role: Podman

An Ansible role that installs Podman on Ubuntu.

## Requirements

None.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable       | Default Value        | Description                                                                 |
| -------------- | -------------------- | --------------------------------------------------------------------------- |
| `podman_state` | `present`            | State of the packages (`present`, `latest`, `absent`).                      |
| `podman_user`  | `{{ ansible_user }}` | The user for whom lingering and the rootless podman socket will be enabled. |

## Dependencies

None.

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
cd roles/podman
molecule test
```

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: podman
      vars:
        podman_user: myuser
```
