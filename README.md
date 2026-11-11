# Notes Terraform Repair

This repository holds the Terraform configuration for the CloudNotes notes API. It uses the built-in `terraform_data` resource, so it needs **no cloud provider, no cloud account, no credential, and no billing**. You only run `init`, `validate`, and `plan`. **Never run `terraform apply`.**

## Before timed work

Fork `https://github.com/kalviumcommunity/notes-terraform-repair`, clone your fork and create `fix/terraform-repair`. Use Linux, macOS or WSL2 with Bash, Python 3, Git, curl and Terraform >=1.5 below 2.0. Install CLI for your host architecture (amd64/arm64). No Docker, external provider, cloud login or application service required. Internet needed for GitHub and initial tool installation. Free editor and PDF exporter suffice.

```bash
bash scripts/prerequisites.sh
```

Start timing only after `SETUP READY`. Helper checks Terraform version and performs init/validate on separate built-in-provider probe without changing repair files. Assessed init/validate/plan failures remain intentional until repairs. Never commit generated state/plans; existing tracked state safety remains part of repair. Capture genuine original failures, changes and final checks in PDF. Push only to your personal fork; no upstream PR. Setup errors are not assessed faults.

## Layout

```text
terraform/
├── versions.tf          # Terraform version constraint, local state
├── main.tf              # root configuration: calls two modules
├── variables.tf         # root input variables
├── outputs.tf           # root outputs
└── modules/
    ├── network/         # main.tf, variables.tf, outputs.tf
    └── service/         # main.tf, variables.tf, outputs.tf
```

## Intended design

1. **Root variables.** The root configuration declares three input variables in `variables.tf`, each with a type, a description, and a default:
   | Variable | Type | Default |
   |---|---|---|
   | `environment` | `string` | `"local"` |
   | `region` | `string` | `"us-central1"` |
   | `app_port` | `number` | `5000` |
2. **Module wiring.** The `service` module attaches to the network created by the `network` module. The network's ID is passed from the `network` module output into the `service` module input. The `service` module exposes its endpoint (the `local.endpoint` value) as an output named `endpoint`, and the root `outputs.tf` reads it.
3. **State safety.** Terraform state files, state backups, plan files, and the `.terraform/` folder are never tracked by Git. `.gitignore` prevents them from being added again.

## Commands (run from the repository root)

```bash
terraform -chdir=terraform init -backend=false
terraform -chdir=terraform fmt -check -recursive
terraform -chdir=terraform validate
terraform -chdir=terraform plan -out=tfplan
```

Check what Git tracks:

```bash
git status --short
git ls-files
```

## Rules

- The repository has exactly three planted issues. Fix them in task order so the intended design is met. One issue may hide the next.
- Do not change resource names, module names, or module `main.tf` files.
- Do not hardcode values that the design says come from a variable or a module output.
- Never run `terraform apply`. Never add a cloud provider or credential.
