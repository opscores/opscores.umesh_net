# Validator Role

Deploy Umesh validator node in isolated mode (RPC only on localhost).

## Requirements
- Ubuntu 24.04+ or Debian 12+
- Docker 24+ with Compose v2
- Minimum 4GB RAM, 2 CPU cores

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `chain_id` | `umesh-testnet-1` | Blockchain network ID |
| `moniker` | `my-validator` | Validator name |
| `min_gas_price` | `0.0025` | Minimum gas price |
| `validator_p2p_bind_ip` | `0.0.0.0` | P2P bind address |
| `validator_rpc_bind_ip` | `127.0.0.1` | RPC bind (localhost only) |

## Example Playbook

```yaml
- hosts: validators
  roles:
    - role: opscores.umesh_net.validator
      vars:
        chain_id: "umesh-mainnet-1"
        moniker: "production-validator"
```

## Tags

- `init` - Initialize the validator node
- `config` - Create/update .env configuration
- `start` - Launch docker compose
- `check` - Verify node status
- `firewall` - Configure UFW rules