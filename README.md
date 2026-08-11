# Umesh Net Ansible Collection

Ansible collection for deploying the **Validator + Sentry + RPC** node topology.

Supports **Ubuntu 24/26** and **Debian 12/13** only.

## Structure

- `roles/preflight` - Pre-flight checks and installation of all prerequisites (Docker, just, jq, curl, git) before deployment
- `roles/common` - Common setup tasks shared by all node types (repo clone, Docker image build, umeshctl build)
- `roles/validator` - Validator node role (private; RPC only on localhost)
- `roles/sentry` - Sentry node role (public P2P/RPC gateway for the validator)
- `roles/rpc` - RPC node role (public access endpoints)
- `playbooks/` - Ready-to-use deployment playbooks

## Requirements

- Ubuntu 24.04 LTS / 26.04 LTS or Debian 13 (trixie)
- Root or sudo access
- SSH access to target hosts

## Prerequisites Installed by Preflight Role

| Package     | Purpose                          |
|-------------|----------------------------------|
| docker-ce   | Container runtime                |
| just        | Task runner (umeshctl + compose) |
| curl        | Health checks, downloads         |
| jq          | JSON parsing in shell tasks      |
| git         | Repository clone                 |

## Usage

### Single Role Deployment

```bash
# Deploy Validator (runs preflight + common + validator roles)
ansible-playbook opscores.umesh_net.deploy_validator -i inventory.ini

# Deploy Sentry (runs preflight + common + sentry roles)
ansible-playbook opscores.umesh_net.deploy_sentry -i inventory.ini

# Deploy RPC (runs preflight + common + rpc roles)
ansible-playbook opscores.umesh_net.deploy_rpc -i inventory.ini
```

### Deploy Full Topology

```bash
# Deploy all three nodes in order: Validator → Sentry → RPC
ansible-playbook opscores.umesh_net.deploy_all -i inventory.ini
```

### Verify Deployment

```bash
# Run post-deployment verification checks
ansible-playbook opscores.umesh_net.verify -i inventory.ini
```

## Role Details

### preflight

- Validates OS support (Ubuntu 24/26, Debian 12/13)
- Installs Docker CE (daemon start + enable)
- Installs `just`, `jq`, `curl`, `git` via apt
- Verifies node-umesh repository and umeshctl binary exist
- Checks that the Docker image is already built

### common

- Validates OS support
- Clones the `node-umesh` repository
- Builds the Docker image (`umesh-node:{version}`)
- Builds `umeshctl` via `just build-cli`

### validator

- Creates `.env.validator` from Jinja2 template
- Runs `umeshctl setup plan` (genesis mode) or joins existing chain (join mode)
- Launches node with `docker compose --profile validator up -d`
- Verifies block height is increasing

### sentry

- Creates `.env.sentry` from Jinja2 template
- Runs `umeshctl setup init --role sentry`
- Launces with `docker compose --profile sentry up -d`
- Verifies peer count and health

### rpc

- Creates `.env.rpc` from Jinja2 template
- Runs `umeshctl setup init --role rpc`
- Launches with `docker compose --profile rpc up -d`
- Verifies health and peer count

## Inventory Variables

| Variable                 | Role       | Description                            |
|--------------------------|------------|----------------------------------------|
| `node_mode`              | validator  | Set to `validator`                     |
| `validator_mode`         | validator  | `genesis` or `join`                    |
| `sentinel_ip`            | validator  | IP of sentry peer firewall allows     |
| `join_genesis_url`       | validator  | Genesis file URL (join mode)           |
| `persistent_peers_for_join` | validator | Comma-separated peer list (join mode)|
| `validator_node_id`      | sentry     | Validator P2P node ID                  |
| `validator_ip`           | sentry     | Validator IP                           |
| `sentry_node_id`         | rpc        | Sentry P2P node ID                     |
| `sentry_ip`              | rpc        | Sentry IP                              |
