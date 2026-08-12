# Umesh Net Ansible Collection

Ansible collection for deploying the **Genesis + Validator + Sentry + RPC** node topology.

Supports **Ubuntu 24/26** and **Debian 12/13** only.

## Structure

- `roles/preflight` - Pre-flight checks and installation of all prerequisites (Docker, just, jq, curl, git)
- `roles/common` - Common setup tasks shared by all node types (repo clone, Docker image build, umeshctl build)
- `roles/genesis` - Genesis validator role (creates a new blockchain)
- `roles/validator` - Validator role (joins an existing blockchain)
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

### Deploy Genesis (creates a new blockchain)

```bash
ansible-playbook opscores.umesh_net.deploy_genesis -i inventory.ini
```

### Deploy Validator (joins existing blockchain)

```bash
ansible-playbook opscores.umesh_net.deploy_validator -i inventory.ini
```

### Deploy Sentry

```bash
ansible-playbook opscores.umesh_net.deploy_sentry -i inventory.ini
```

### Deploy RPC

```bash
ansible-playbook opscores.umesh_net.deploy_rpc -i inventory.ini
```

### Deploy Full Topology

```bash
# Deploy all nodes in order: Genesis → Validator → Sentry → RPC
ansible-playbook opscores.umesh_net.deploy_all -i inventory.ini
```

### Verify Deployment

```bash
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

### genesis

- Creates new blockchain via `umeshctl setup plan`
- Configures validator parameters (commission, stake, moniker)
- Launches node with `docker compose --profile validator up -d`
- Verifies block height is increasing

### validator

- Joins existing blockchain via `umeshctl setup init --role join`
- Downloads genesis.json from configured source
- Launches node with `docker compose --profile validator up -d`
- Verifies block height is increasing

### sentry

- Creates `.env.sentry` from Jinja2 template
- Runs `umeshctl setup init --role sentry`
- Launches with `docker compose --profile sentry up -d`
- Verifies peer count and health

### rpc

- Creates `.env.rpc` from Jinja2 template
- Runs `umeshctl setup init --role rpc`
- Launches with `docker compose --profile rpc up -d`
- Verifies health and peer count

## Inventory Variables

| Variable                 | Role        | Description                              |
|--------------------------|-------------|------------------------------------------|
| `sentinel_ip`            | genesis     | IP of sentry peer for firewall rules     |
| `genesis_plan_config`    | genesis     | Path to genesis plan YAML config         |
| `join_genesis_url`       | validator   | Genesis file URL for download            |
| `persistent_peers_for_join` | validator | Comma-separated peer list               |
| `validator_node_id`      | sentry      | Validator P2P node ID                    |
| `validator_ip`           | sentry      | Validator IP                             |
| `sentry_node_id`         | rpc         | Sentry P2P node ID                       |
| `sentry_ip`              | rpc         | Sentry IP                                |