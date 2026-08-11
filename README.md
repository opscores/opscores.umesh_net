# Umesh Net Ansible Collection

Ansible collection for deploying the **Validator + Sentry + RPC** node topology as described in MINSTART.md.

## Structure

- `roles/common` - Common setup tasks shared by all node types
- `roles/validator` - Validator node role (private, no public ports)
- `roles/sentry` - Sentry node role (public, protects validator)
- `roles/rpc` - RPC node role (public access endpoints)
- `playbooks/` - Ready-to-use deployment playbooks

## Usage

```bash
# Deploy all nodes
ansible-playbook opscores.umesh_net.deploy_all

# Deploy specific role
ansible-playbook opscores.umesh_net.deploy_validator
```

Reference: `MINSTART.md`