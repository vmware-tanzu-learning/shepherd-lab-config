# shepherd-lab-config

Configuration for deploying Tanzu Platform lab environments using the generic-deployer and post-deployer shepherd modules. Each product is configured through [om](https://github.com/pivotal-cf/om) ops files, optional Terraform, and optional post-deploy scripts.

## Layout

| Path | Purpose |
| --- | --- |
| `full-<version>.json` | Manifest listing the products to deploy for a given release (ops files, certs, Terraform, NAT VM tags, post-deploy scripts). |
| `general/` | Ops files applied to every environment (`bosh-ops.yml` for the BOSH director, `cf-ops.yml` for TAS). |
| `hub/<version>/` | Tanzu Hub tile configuration. |
| `genai-for-tas/<version>/` | GenAI tile configuration. |
| `vmware-postgres-for-tas/<version>/` | VMware Postgres tile configuration. |
| `tas-hub-collector/<version>/` | Hub TAS collector tile configuration. |

Product directories are versioned (`10.4.3`, `10.4.4`, `10.4.5`) and each `full-*.json` pins the versions it uses.

## Manifest fields

Each entry in `full-<version>.json` supports:

- `name`, `slug`, `version`: the product name, its directory under this repo, and the version directory to use.
- `ops_files`: ops files applied to the product's staged config.
- `certs` / `cert_domains`: whether to generate certificates, and for which domains.
- `terraform`: whether to apply the product's `tf/` directory.
- `nat_vm_tags`: VM tags that need outbound NAT access.
- `skip_config` / `post_deploy_scripts`: skip the default config step and run the listed scripts after deploy.

## Adding a new version

1. Copy the previous version directory for each product that changed.
2. Update the ops files and scripts.
3. Add a `full-<version>.json` that points at the new versions.
