# Ansible Role: Network

This role manages network configuration on Ubuntu using Netplan and configures kernel parameters for unprivileged port binding.

## Features
- **Static IP Assignment**: Configure multiple interfaces with static IPs, gateways, and DNS.
- **Rootless Podman Support**: Optionally set `net.ipv4.ip_unprivileged_port_start` to allow non-root users to bind to privileged ports.
- **Validation**: Automatically validates Netplan syntax before applying changes.

## Requirements
- Ubuntu 18.04+ (Netplan support)
- `become` privileges (root access)

## Role Variables

| Variable | Description | Required | Default |
| :--- | :--- | :--- | :--- |
| `network_interfaces` | List of interface configurations. See [Interface Schema](#interface-schema). | Yes | *None* |
| `network_unprivileged_port_start` | Lowest port number for unprivileged users. | No | *OS Default* |

### Interface Schema

| Key | Description | Required |
| :--- | :--- | :--- |
| `name` | The name of the network interface (e.g., `eth0`). | Yes |
| `addresses` | List of IP addresses in CIDR notation. | Yes |
| `gateway4` | IPv4 gateway address. | No |
| `nameservers` | List of DNS nameserver IP addresses. | No |

### Interface Example
```yaml
- name: eth0
  addresses:
    - 192.168.1.10/24
  gateway4: 192.168.1.1
  nameservers:
    - 1.1.1.1
    - 8.8.8.8
```

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: network
      vars:
        network_unprivileged_port_start: 80
        network_interfaces:
          - name: enp0s3
            addresses:
              - 10.0.2.15/24
            gateway4: 10.0.2.2
            nameservers:
              - 8.8.8.8
```

## Security Considerations
- Netplan configuration files are created with `0600` permissions.
- Lowering the unprivileged port start should be done carefully; only lower it as much as necessary (e.g., to 80).
