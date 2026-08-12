# Common Role

Repository handling, image building, and CLI compilation for Umesh nodes.

## Requirements
- Ubuntu 24.04+ or Debian 12+
- Git
- Go (for building umeshctl)

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `repo_url` | `https://github.com/umesh-network/umesh-node` | Node repository URL |
| `repo_dest` | `{{ ansible_tmpdir }}/umesh-node` | Destination path for cloned repository |

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