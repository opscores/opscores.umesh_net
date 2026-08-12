# RPC Role

Deploy Umesh RPC node (public RPC gateway).

## Requirements
- Ubuntu 24.04+ or Debian 12+
- Docker 24+ with Compose v2
- Minimum 4GB RAM, 2 CPU cores

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `chain_id` | `umesh-testnet-1` | Blockchain network ID |
| `moniker` | `my-rpc` | RPC node name |
| `min_gas_price` | `0.0025` | Minimum gas price |
| `rpc_p2p_bind_ip` | `0.0.0.0` | P2P bind address |
| `rpc_rpc_bind_ip` | `0.0.0.0` | RPC bind address |

## Example Playbook

```yaml
- hosts: rpc-nodes
  roles:
    - role: opscores.umesh_net.rpc
      vars:
        moniker: "production-rpc"
```

## Tags

- `init` - Initialize the RPC node
- `config` - Create/update .env configuration
- `start` - Launch docker compose
- `check` - Verify RPC health endpoint
- `firewall` - Configure UFW rules