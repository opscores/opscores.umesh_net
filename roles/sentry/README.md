# Sentry Role

Deploy Umesh sentry node (public gateway).

## Requirements
- Ubuntu 24.04+ or Debian 12+
- Docker 24+ with Compose v2
- Minimum 4GB RAM, 2 CPU cores

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `chain_id` | `umesh-testnet-1` | Blockchain network ID |
| `moniker` | `my-sentry` | Sentry node name |
| `min_gas_price` | `0.0025` | Minimum gas price |
| `sentry_p2p_bind_ip` | `0.0.0.0` | P2P bind address |
| `sentry_rpc_bind_ip` | `0.0.0.0` | RPC bind address |

## Example Playbook

```yaml
- hosts: sentinels
  roles:
    - role: opscores.umesh_net.sentry
      vars:
        moniker: "production-sentry"
```

## Tags

- `init` - Initialize the sentry node
- `config` - Create/update .env configuration
- `start` - Launch docker compose
- `check` - Verify peer connectivity
- `firewall` - Configure UFW rules