# Common Role

Repository handling, Docker image pulling, and CLI download for Umesh nodes.

## Requirements
- Ubuntu 24.04+ or Debian 12+
- Git
- Docker (for containerized node runtime)

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `repo_url` | `https://github.com/opscores/umesh-node` | Node repository URL |
| `repo_dest` | `/opt/umesh-node` | Destination path for cloned repository |

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: opscores.umesh_net.common
      vars:
        repo_url: "git@github.com:your-org/umesh-node.git"
```

## Tags

- `common` - Include common node setup
- `build` - Build CLI and Docker images