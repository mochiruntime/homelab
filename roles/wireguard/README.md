# Ansible Role: WireGuard

An Ansible role that deploys WireGuard as a rootless Podman container managed by systemd Quadlets.

## Requirements

- The target system must have Podman installed (the [podman role](../podman/) can be used to install it).
- A wireguard user must exist on the host (the [podman_user role](../podman_user/) can be used to create it).
- IP forwarding and source valid mark must be enabled on the host (the [network role](../network/) can be used to enable these settings):
  - `net.ipv4.ip_forward = 1`
  - `net.ipv4.conf.all.src_valid_mark = 1`

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml` and `meta/argument_specs.yml`):

| Variable | Default Value | Description |
| --- | --- | --- |
| `wireguard_user` | `wireguard` | The host user that will run the rootless container. |
| `wireguard_image` | `lscr.io/linuxserver/wireguard:latest` | The WireGuard container image to use. |
| `wireguard_port` | `51820` | The UDP port to expose for WireGuard (the `wireguard_user` must be able to bind to this port). |
| `wireguard_server_url` | **Required** | External IP or DNS name for the VPN server. |
| `wireguard_data_dir` | `"/home/{{ wireguard_user }}/config"` | Host directory for configuration and peer data. |
| `wireguard_peers` | `1` | Number of peers to generate or a list of peer names. |
| `wireguard_timezone` | `"UTC"` | Timezone for the container (prefer using TZ database names). |
| `wireguard_peer_dns` | `"auto"` | DNS server(s) used by peers. |
| `wireguard_internal_subnet` | `"10.13.13.0"` | Internal subnet for the VPN. |
| `wireguard_allowed_ips` | `"0.0.0.0/0"` | Defines the network ranges routed through the tunnel and specifies the authorized source IPs for each peer to prevent IP spoofing. |

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
cd roles/wireguard
molecule test
```

## Example Playbook

```yaml
- name: Deploy WireGuard
  hosts: all
  become: true
  roles:
    - role: podman

    - role: podman_user
      vars:
        podman_user_list: ["wireguard"]

    - role: network
      vars:
        network_sysctl_settings:
          net.ipv4.ip_forward: 1
          net.ipv4.conf.all.src_valid_mark: 1

    - role: wireguard
      vars:
        wireguard_server_url: "vpn.example.com"
        wireguard_peers: 3
        wireguard_allowed_ips: "192.168.1.0/24, 10.13.13.0/24"
```
