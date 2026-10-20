# shepherd-lab-config

Configuration for deploying Tanzu products onto Tanzu Application Service (TAS) lab environments created with Shepherd. Each product is configured through [om](https://github.com/pivotal-cf/om) ops files, optional Terraform, and optional post-deploy scripts.

## Layout

| Path | Purpose |
| --- | --- |
| `full-<version>.json` | Manifest listing the products to deploy for a given release (ops files, certs, Terraform, NAT VM tags, post-deploy scripts). |
| `general/` | Ops files applied to every environment (`bosh-ops.yml` for the BOSH director, `cf-ops.yml` for TAS). |
| `hub/<version>/` | Tanzu Hub ops file and Terraform (`tf/`) for load balancer and networking resources. |
| `genai-for-tas/<version>/` | GenAI on TAS ops file. |
| `vmware-postgres-for-tas/<version>/` | VMware Postgres for TAS ops file. |
| `tas-hub-collector/<version>/` | Hub TAS collector ops file plus post-deploy scripts. |

Product directories are versioned (`10.4.3`, `10.4.4`, `10.4.5`) and each `full-*.json` pins the versions it uses.

## Manifest fields

Each entry in `full-<version>.json` supports:

- `name`, `slug`, `version`: the product name, its directory under this repo, and the version directory to use.
- `ops_files`: ops files applied to the product's staged config.
- `certs` / `cert_domains`: whether to generate certificates, and for which domains.
- `terraform`: whether to apply the product's `tf/` directory.
- `nat_vm_tags`: VM tags that need outbound NAT access.
- `skip_config` / `post_deploy_scripts`: skip the default config step and run the listed scripts after deploy.

## Variables

Ops files use `((variable))` placeholders that the deployment tooling fills in at run time, such as `((single_az))`, `((az_name_list))` and `((environment_tag))`. Scripts expect `ENV_NAME`, `CONFIG_DIR`, `PRODUCT_SLUG`, `PRODUCT_VERSION` and the Ops Manager settings (`OM_HOSTNAME`, `OM_USERNAME`, `OM_PASSWORD`) in the environment. Hostnames follow the pattern `*.${ENV_NAME}.cf-app.com`.

No credentials are stored in this repository. Secrets are generated or fetched from Ops Manager during deployment.

## Hub iframe embedding

`tas-hub-collector/<version>/scripts/lib/iframe-embed-overlays.sh` makes Tanzu Hub embeddable in a cross-site iframe, for use in Educates workshops. It installs ytt overlays that relax frame headers, set `SameSite=None; Secure` on the UAA login cookies, and restrict framing with a CSP `frame-ancestors` rule. It is idempotent and should run after every Tanzu SM install. See the header comments in the script for details.

## Adding a new version

1. Copy the previous version directory for each product that changed.
2. Update the ops files and scripts.
3. Add a `full-<version>.json` that points at the new versions.

## Status

This is a lab and learning configuration, not an officially supported deployment guide.
