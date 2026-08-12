# Preflight Role

Automated prerequisite checks and installations for Umesh nodes.

## Requirements
- Ubuntu 24.04+ or Debian 12+

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `firewall_enabled` | `true` | Whether to configure UFW firewall |
| `firewall_ssh_port` | `22` | SSH port for UFW |

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: opscores.umesh_net.preflight
```

## Tags

- `common` - Include common node setup
- `preflight` - Run prerequisite checks and installations
- `firewall` - Configure UFW rules