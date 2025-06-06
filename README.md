# Simple Terraform CD GitHub Action

This GitHub Action runs `terraform init`, `terraform plan`, and `terraform apply` against a specified environment, using backend and variable files matched to that environment.

> Label your backend config file for terraform init to remote backend as "backend.<env>.conf". 

## Features

- Backend initialization to remote backend depending on environment e.g. dev, prod, staging
- Scoped `terraform plan` using env-specific `.tfvars`
- Generates a `tfplan.json` for review
- Applies the plan automatically
- Exports current state to `tfstate.json` and `data.json`

## Prerequisites

### Required GitHub Secrets

Ensure the following secrets are configured in your GitHub repository or environment:

| Secret Name                     | Description                                                                                       |
|----------------------------------|---------------------------------------------------------------------------------------------------|
| `GCP_WORKLOAD_IDENTITY_PROVIDER` | The full Workload Identity Provider resource name. Example: `projects/123456789/locations/global/workloadIdentityPools/github/providers/my-provider` |
| `GCP_IMPERSONATOR_SA`            | Service account email to impersonate via Workload Identity Federation. Example: `github-ci@your-project.iam.gserviceaccount.com` |

> Ensure the impersonator service account has the `roles/iam.serviceAccountTokenCreator` role on the deployment service account.

## How to run this in your GitHub Actions pipeline

Add the following to your workflow to run CI checks on your Terraform configuration. Note that you must have the secrets enabled in your GitHub repo. This will be used for OIDC access to your project's deployment role, which contains all the roles and permissions used to deploy your infrastructure.

```yaml
- name: Run Terraform CD
  uses: augustinechiajh/gha-cd-tf/.github/workflows/terraform-deployment-gcp.yaml@main
  with:
    environment: 'dev'
    terraform-work-dir: 'iac/tf' # specify your own directory, iac/tf is default
    is-destroy: false # default is false, set to true if you want to destroy terraform provisioned infra
    debug-logs: false # default is false, set to true for TF_LOG=DEBUG enabled
  secrets:
    GCP_WORKLOAD_IDENTITY_PROVIDER: ${{ secrets.GCP_WORKLOAD_IDENTITY_PROVIDER }}
    GCP_IMPERSONATOR_SA: ${{ secrets.GCP_IMPERSONATOR_SA }}