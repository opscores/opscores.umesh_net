# Umesh Net Ansible Collection

Ansible collection for deploying the **Genesis + Validator + Sentry + RPC** node topology.

Supports **Ubuntu 24/26** and **Debian 12/13** only.

## 📦 Structure

- `roles/preflight` - Pre-flight checks and installation of all prerequisites (Docker, just, jq, curl, git)
- `roles/common` - Common setup tasks shared by all node types (repo clone, Docker image pull, umeshctl download)
- `roles/backup` - **New**: Role for creating backups of validator/sentry keys and configuration
- `roles/genesis` - Genesis validator role (creates a new blockchain)
- `roles/validator` - Validator role (joins an existing blockchain)
- `roles/sentry` - Sentry node role (public P2P/RPC gateway for the validator)
- `roles/rpc` - RPC node role (public access endpoints)
- `playbooks/` - Ready-to-use deployment playbooks

## ✅ Requirements

- Ubuntu 24.04 LTS / 26.04 LTS or Debian 13 (trixie)
- Root or sudo access
- SSH access to target hosts

## 🔧 Prerequisites Installed by Preflight Role

| Package     | Purpose                          |
|-------------|----------------------------------|
| docker-ce   | Container runtime                |
| curl        | Health checks, downloads         |
| jq          | JSON parsing in shell tasks      |
| git         | Repository clone                 |
| just (opt.) | Task runner for manual convenience|

## 🛠 Role Details

### preflight

- Validates OS support (Ubuntu 24/26, Debian 12/13)
- Installs Docker CE (daemon start + enable)
- Installs `just`, `jq`, `curl`, `git` via apt
- Verifies umesh-node repository exists and umeshctl binary is installed
- Checks that the Docker image is already built

### common

- Validates OS support
- Clones the `umesh-node` repository
- Pulls the Docker image (`ghcr.io/opscores/umesh-node:latest`) from GHCR
- Downloads `umeshctl` release binary from the [umesh-cli](https://github.com/opscores/umesh-cli) repository
- Downloads the genesis plan YAML from umesh-cli examples

### genesis

- Creates new blockchain via `umeshctl setup plan`
- Configures validator parameters (commission, stake, moniker)
- Launches node with `docker compose --profile validator up -d`
- Verifies block height is increasing
- **New**: Includes `wasm_memory_cache_size`, `wasm_simulation_gas_limit`, pruning settings

### validator

- Joins existing blockchain via `umeshctl setup init --role validator`
- Downloads genesis.json from configured source
- Launches node with `docker compose --profile validator up -d`
- Verifies block height is increasing
- **New**: Includes `wasm_memory_cache_size`, `wasm_simulation_gas_limit`, pruning settings, p2p limits
- **Critical fix**: `.env` file is created **BEFORE** initialization to prevent failures

### sentry

- Creates `.env.sentry` from Jinja2 template
- Runs `umeshctl setup init --role sentry`
- Launches with `docker compose --profile sentry up -d`
- Verifies peer count and health
- **New**: Includes `wasm_memory_cache_size`, `wasm_simulation_gas_limit`, pruning settings

### rpc

- Creates `.env.rpc` from Jinja2 template
- Runs `umeshctl setup init --role rpc`
- Launches with `docker compose --profile rpc up -d`
- Verifies health and peer count
- **New**: Includes `wasm_memory_cache_size`, `wasm_simulation_gas_limit`, pruning settings

### backup **(NEW)**

- Creates backups of validator/sentry keys (`priv_validator_key.json`, `node_key.json`)
- Backs up RPC configuration (`config.toml`, `app.toml`)
- Configurable retention policy (default: 7 backups)
- Compresses backup directory into `.tar.gz`
- Run with: `ansible-playbook -i inventory.ini roles/backup`

## 🚀 Usage

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

Deploy all nodes in order: Genesis → Validator → Sentry → RPC

```bash
ansible-playbook opscores.umesh_net.deploy_all -i inventory.ini
```

### Verify Deployment

```bash
ansible-playbook opscores.umesh_net.verify -i inventory.ini
```

### Create Key Backup

```bash
ansible-playbook -i inventory.ini roles/backup
```

## 📋 Inventory Variables

| Variable                 | Role        | Description                              |
|--------------------------|-------------|------------------------------------------|
| `repo_dest`              | common      | Repository clone path (default: /opt/umesh-node) |
| `docker_image`           | common      | Pre-built Docker image (default: ghcr.io/opscores/umesh-node:latest) |
| `umeshctl_path`         | common      | umeshctl binary path (default: /usr/local/bin/umeshctl) |
| `sentinel_ip`            | genesis     | IP of sentry peer for firewall rules     |
| `genesis_plan_config`    | genesis     | Path to genesis plan YAML config         |
| `join_genesis_url`       | validator   | Genesis file URL for download            |
| `persistent_peers_for_join` | validator | Comma-separated peer list               |
| `validator_node_id`      | sentry      | Validator P2P node ID                    |
| `validator_ip`           | sentry      | Validator IP                             |
| `sentry_node_id`         | rpc         | Sentry P2P node ID                       |
| `sentry_ip`              | rpc         | Sentry IP                                |
| `backup_dir_base`        | backup      | Base path for backups (default: /opt/umesh-backups) |
| `backup_retention`       | backup      | Number of backups to keep (default: 7)   |

## ⚙️ Role Variables (Cosmos SDK Settings)

All validator, sentry, and rpc roles now include these Cosmos SDK parameters in `defaults/main.yml`:

| Variable | Default | Description |
|----------|---------|-------------|
| `min_gas_price` | `0.0025uumesh` | Minimum gas price |
| `wasm_memory_cache_size` | `1024` | Wasm memory cache size (MiB) |
| `wasm_simulation_gas_limit` | `50000000` | Simulation gas limit |
| `pruning_strategy` | `custom` | Pruning strategy |
| `pruning_keep_recent` | `1000` | Keep recent blocks |
| `pruning_interval` | `100` | Pruning interval |
| `p2p_max_inbound_peers` | `50` | Max inbound P2P peers |
| `p2p_max_outbound_peers` | `10` | Max outbound P2P peers |

## 🛡 Example Playbook with Vault

```yaml
- hosts: validators
  become: true
  vars:
    chain_id: "umesh-mainnet-1"
    moniker: "production-validator"
  roles:
    - role: opscores.umesh_net.validator
  # Run with vault: ansible-playbook deploy_validator.yml --ask-vault-pass
```