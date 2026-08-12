# Genesis Role

Create a new Umesh blockchain (genesis block).

## Requirements
- Ubuntu 24.04+ or Debian 12+
- Docker 24+ with Compose v2
- Minimum 4GB RAM, 2 CPU cores

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `chain_id` | `umesh-testnet-1` | Blockchain network ID |
| `denom` | `uumesh` | Native token denomination |
| `min_gas_price` | `0.0025` | Minimum gas price |
| `moniker` | `my-genesis-validator` | Validator moniker |
| `validator_name` | `validator` | Validator name |

## Example Playbook

```yaml
- hosts: genesis
  roles:
    - role: opscores.umesh_net.genesis
      vars:
        moniker: "genesis-validator"
        stake_amount: "1000000000000"
```

## Tags

- `init` - Run genesis plan setup
- `config` - Create/update .env configuration
- `start` - Launch validator node with docker compose
- `check` - Verify validator RPC status
- `firewall` - Configure UFW rules