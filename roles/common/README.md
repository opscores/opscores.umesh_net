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
| `docker_image` | `ghcr.io/opscores/umesh-node:latest` | Pre-built Docker image (pulled from GHCR) |
| `docker_image_registry` | `ghcr.io/opscores` | Docker image registry |
| `docker_image_name` | `umesh-node` | Docker image name |
| `docker_image_tag` | `latest` | Docker image tag |
| `umeshctl_version` | `v0.2.0` | umeshctl release version (binary download) |
| `umeshctl_path` | `/usr/local/bin/umeshctl` | umeshctl binary install path |
| `umeshcli_repo` | `https://github.com/opscores/umesh-cli` | umesh-cli repository (release binaries) |
| `genesis_plan_config` | `{{ repo_dest }}/genesis-plan.yaml` | Path to genesis plan YAML (downloaded from umesh-cli) |
| `node_config_dir` | `{{ repo_dest }}/config` | Node config output directory (v0.2.0) |
| `keyring_pass_file` | `{{ repo_dest }}/.keyring-pass` | Keyring password file (v0.2.0) |
| `keyring_password` | `change-me-secure` | Plain-text password written to keyring_pass_file |

## umeshctl v0.2.0 Notes

The common role prepares two artifacts required by umeshctl v0.2.0:

1. **Node config directory** (`node_config_dir`) — where each role renders its YAML config template.
2. **Keyring password file** (`keyring_pass_file`) — plain-text password consumed by `umeshctl init --keyring-password-file`.

The genesis plan YAML and umeshctl binary are downloaded from the [umesh-cli repository](https://github.com/opscores/umesh-cli) release matching `umeshctl_version`.

## Example Playbook

```yaml
- hosts: all
  roles:
    - role: opscores.umesh_net.common
      vars:
        repo_url: "git@github.com:your-org/umesh-node.git"
        umeshctl_version: "v0.2.0"
        keyring_password: "my-secure-password"
```

## Tags

- `common` - Include common node setup
- `build` - Docker pull + CLI download
- `cli` - Download umeshctl binary + genesis-plan.yaml
- `config` - Create config dir + keyring password file
