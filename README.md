# Safe Policy Rollouts with GitOps
KubeCon 2021 - Governance as Code Day with Cloud Custodian hosted by Stacklet

![Example](example.png)

## Installation

To install, first generate a Github personal access token with access to the repos
that you want to run CI on. Then, run the following commands:

```bash
cd deploy
terraform init
cp settings.tfvars.example settings.tfvars
# Edit the settings.tfvars file, all vars can be found in deploy/vars.tf
terraform apply -var-file=settings.tfvars
```

This project uses [c7n-policystream](https://cloudcustodian.io/docs/tools/c7n-policystream.html)
to detect changes between your commit and the base branch, as defined by `base_branch`. The
CodeBuild job then runs the changed policies as well as the original ones from `base_branch`
and compares the results of the two. You can also specify thresholds for the job to fail on,
with values for both total number of resources (e.g. if the delta is 5 resources for a given
policy, fail) or for a percentage (e.g. if the delta in percentage is greater than 50% fail).

<!-- BEGIN_TF_DOCS -->
## Requirements

No requirements.

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | n/a |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [aws_codebuild_project.policy_ci](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/codebuild_project) | resource |
| [aws_codebuild_webhook.policy_ci](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/codebuild_webhook) | resource |
| [aws_iam_policy.codebuild_access](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_policy) | resource |
| [aws_iam_role.codebuild_executor](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_iam_role_policy_attachment.codebuild_access](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy_attachment) | resource |
| [aws_iam_role_policy_attachment.codebuild_executor](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role_policy_attachment) | resource |
| [aws_s3_bucket.c7n_ci](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket) | resource |
| [aws_s3_bucket_object.accounts_yaml](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_object) | resource |
| [aws_s3_bucket_object.buildspec](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_object) | resource |
| [aws_s3_bucket_object.parse_output](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_object) | resource |
| [aws_s3_bucket_object.requirements](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_object) | resource |
| [aws_s3_bucket_object.resolve_base](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_object) | resource |
| [aws_secretsmanager_secret.github_token](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/secretsmanager_secret) | resource |
| [aws_secretsmanager_secret_version.github_token](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/secretsmanager_secret_version) | resource |
| [aws_caller_identity.current](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/caller_identity) | data source |
| [aws_iam_policy_document.codebuild_access](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/iam_policy_document) | data source |
| [aws_region.current](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/region) | data source |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_accounts_yaml"></a> [accounts\_yaml](#input\_accounts\_yaml) | Absolute path to c7n-org accounts.yaml | `string` | n/a | yes |
| <a name="input_base_branch"></a> [base\_branch](#input\_base\_branch) | Base Branch | `string` | n/a | yes |
| <a name="input_bucket"></a> [bucket](#input\_bucket) | Pass in a bucket to use the bucket for storing scripts, else one is created | `string` | `""` | no |
| <a name="input_buildspec"></a> [buildspec](#input\_buildspec) | Location of buildspec.yaml in repository | `string` | `"buildspec.yaml"` | no |
| <a name="input_c7n_org_version"></a> [c7n\_org\_version](#input\_c7n\_org\_version) | C7N Org Version | `string` | `"0.6.12"` | no |
| <a name="input_c7n_version"></a> [c7n\_version](#input\_c7n\_version) | C7N Version | `string` | `"0.9.13"` | no |
| <a name="input_ci_policy_arn"></a> [ci\_policy\_arn](#input\_ci\_policy\_arn) | CI Role Policy ARN, defaults to ReadOnlyAccess | `string` | `"arn:aws:iam::aws:policy/ReadOnlyAccess"` | no |
| <a name="input_github_api_url"></a> [github\_api\_url](#input\_github\_api\_url) | Github API Url | `string` | `"https://api.github.com"` | no |
| <a name="input_github_repo"></a> [github\_repo](#input\_github\_repo) | Github Repo Name | `string` | n/a | yes |
| <a name="input_github_token"></a> [github\_token](#input\_github\_token) | Github Token to Create Status Checks | `string` | n/a | yes |
| <a name="input_output_dir"></a> [output\_dir](#input\_output\_dir) | Cloud Custodian Output directory | `string` | `"output"` | no |
| <a name="input_policy_dir"></a> [policy\_dir](#input\_policy\_dir) | Policies Directory (relative path from repo root) | `string` | `"policies"` | no |
| <a name="input_policystream_version"></a> [policystream\_version](#input\_policystream\_version) | Policystream Version | `string` | `"0.4.12"` | no |
| <a name="input_repository_url"></a> [repository\_url](#input\_repository\_url) | Policy Repository URL | `string` | n/a | yes |
| <a name="input_resource_threshold"></a> [resource\_threshold](#input\_resource\_threshold) | Policy resource delta threshold | `number` | `10` | no |
| <a name="input_resource_threshold_percent"></a> [resource\_threshold\_percent](#input\_resource\_threshold\_percent) | Policy resource delta threshold percent Valid Values between 0-1 | `number` | `1` | no |
| <a name="input_tags"></a> [tags](#input\_tags) | Tags | `map(string)` | `{}` | no |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_bucket"></a> [bucket](#output\_bucket) | n/a |
| <a name="output_c7n_org_version"></a> [c7n\_org\_version](#output\_c7n\_org\_version) | n/a |
| <a name="output_c7n_version"></a> [c7n\_version](#output\_c7n\_version) | n/a |
| <a name="output_policystream_version"></a> [policystream\_version](#output\_policystream\_version) | n/a |
<!-- END_TF_DOCS -->