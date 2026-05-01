# Ansible Role: Caddy

An Ansible role that deploys Caddy as a rootless Podman container managed by systemd Quadlets.

## Requirements

- The target system must have Podman installed (the [`podman`](../podman/) role is recommended for environment setup).

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable | Default Value | Description |
| --- | --- | --- |
| `caddy_user` | **Required** | The system user that will run the rootless container. |
| `caddy_image` | `docker.io/library/caddy:latest` | The Caddy image to use. |
| `caddy_sites` | `[]` | List of site configurations for the Caddyfile. Each item can have `domain`, `proxy`, and `raw` directives. |
| `caddy_raw_config` | undefined | Full Caddyfile content. If provided, `caddy_sites` is ignored. |
| `caddy_network` | undefined | Optional Podman network name for the container to join. |
| `caddy_data_dir` | `"/home/{{ caddy_user }}/data"` | Host directory for persistent data storage (certificates, etc.). |
| `caddy_config_dir` | `"/home/{{ caddy_user }}/config"` | Host directory for Caddy configuration. |
| `caddy_http_port` | `80` | Host port to map to container port 80. |
| `caddy_https_port` | `443` | Host port to map to container port 443. |
| `caddy_extra_volumes` | `[]` | Additional volumes to mount into the container (e.g., `["/path/on/host:/path/in/container:ro"]`). |

> [!IMPORTANT]
> Since this role runs Caddy in rootless mode, binding to privileged ports (like 80 and 443) requires host-level configuration (e.g., setting `net.ipv4.ip_unprivileged_port_start` to a lower value) or using higher port numbers.

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
cd roles/caddy
molecule test
```

## Example Playbook

```yaml
- name: Deploy Caddy
  hosts: all
  become: true
  roles:
    - role: podman_user
      vars:
        podman_user: caddy
    - role: caddy
      vars:
        caddy_user: caddy
        caddy_sites:
          - domain: "example.com"
            proxy: "localhost:8080"
          - domain: "another.com"
            raw: |
              root * /usr/share/caddy
              file_server
```
