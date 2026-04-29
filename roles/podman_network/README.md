# Ansible Role: Podman Network

An Ansible role that creates and manages rootless Podman networks via systemd Quadlets.

## Requirements

- The target user must have systemd lingering enabled. The `podman` role can handle this.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable              | Default Value | Description                                             |
| --------------------- | ------------- | ------------------------------------------------------- |
| `podman_network_list` | `[]`          | A list of dictionaries defining the networks to manage. |

**Structure of `podman_network_list` items:**

| Key        | Required | Type    | Default   | Description                                                                  |
| ---------- | -------- | ------- | --------- | ---------------------------------------------------------------------------- |
| `name`     | Yes      | string  |           | The name of the Podman network.                                              |
| `user`     | Yes      | string  |           | The system user (rootless) who will own the network.                         |
| `state`    | No       | string  | `present` | Whether the network should be created (`present`) or destroyed (`absent`).   |
| `internal` | No       | boolean | `false`   | If true, isolates the network from external access.                          |
| `subnet`   | No       | string  |           | Custom subnet for the network (e.g., '10.89.2.0/24').                        |
| `gateway`  | No       | string  |           | Custom gateway for the network.                                              |
| `labels`   | No       | list    |           | List of labels to apply to the network (e.g., 'app=proxy').                  |

## Dependencies

- `podman`

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
cd roles/podman_network
molecule test
```

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: podman
      vars:
        podman_user: myuser
    - role: podman_network
      vars:
        podman_network_list:
          - name: proxy-net
            user: myuser
            state: present
            internal: true
            subnet: "10.89.2.0/24"
```
