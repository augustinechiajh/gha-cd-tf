# Simple Terraform CD GitHub Action

This GitHub Action runs `terraform init`, `terraform plan`, and `terraform apply` against a specified environment, using backend and variable files matched to that environment.

Note: Label your backend config file for terraform init to remote backend as "backend.<env>.conf". 

## Features

- Backend initialization to remote backend depending on environment e.g. dev, prod, staging
- Scoped `terraform plan` using env-specific `.tfvars`
- Generates a `tfplan.json` for review
- Applies the plan automatically
- Exports current state to `tfstate.json` and `data.json`

## How to run this in your GitHub Actions pipeline

Add the following to your workflow to run CI checks on your Terraform configuration:

```yaml
- name: Run Terraform CD
  uses: augustinechiajh/gha-cd-tf/.github/workflows/terraform-deployment.yaml@main
  with:
    environment: 'dev'
    terraform-work-dir: 'iac/tf' # specify your own directory, iac/tf is default