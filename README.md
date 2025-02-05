# Terraform Infrastructure Setup

This document outlines the Terraform configurations used for managing infrastructure resources. It provides an overview of the directory structure and the purpose of each component within the "infra/terraform/src" project.

To begin using Terraform, some initial setup is required, including an S3 bucket, a DynamoDB table, Secret Manager, and the values for the necessary variables, which will be passed through the GitHub workflow.

The initial setup will be validated through the GitHub workflow, starting from the "Run Terraform Pre-Check Script" step, as defined in the .github/workflows/infra.yaml file.

The following variable values should be available during the first run of the pipeline.

| Key                                      | Type                | Example Values                                             |
|------------------------------------------|---------------------|------------------------------------------------------------|
| `project`                                | String              | `lscore`                                                   |
| `env`                                    | String              | `""`                                                       |
| `region`                                 | String              | `"us-east-1"`                                              |
| `aws_account_id`                         | String              | `""`                                                       |
| `vpc_cidr`                               | String              | `"172.32.0.0/16"`                                          |
| `private_subnet_cidrs`                   | List of Strings     | `["172.32.1.0/24","172.32.2.0/24","172.32.3.0/24"]`        |
| `public_subnet_cidrs`                    | List of Strings     | `["172.32.4.0/24","172.32.5.0/24","172.32.6.0/24"]`        |
| `availability_zones`                     | List of Strings     | `["us-east-1a", "us-east-1b", "us-east-1c"]`               |
| `ecr_repositories`                       | List of Strings     | `["muni/bondpoint_trading", "muni/tmc_trading", ...]`      |
| `rds_db_name`                            | String              | `"lscoreuat"`                                              |
| `rds_db_cluster_instance_class`          | String              | `""`                                                       |
| `rds_engine`                             | String              | `"postgres"`                                               |
| `rds_engine_version`                     | String              | `"17.1"`                                                   |
| `rds_cluster_parameter_group_family`     | String              | `"postgres17"`                                             |
| `backup_retention_period`                | Integer             | `7`                                                        |


## Directory Structure

The basic directory structure of the Terraform code is as follows:

terraform/src/ 

├── main.tf # Core resources and infrastructure 

├── variables.tf # Variable definitions 

├── outputs.tf # Outputs from the infrastructure 

├── terraform.tfvars # Variable values specific to the environment 

├── modules/ # Reusable Terraform modules 

│   ├── aws-eks/ # EKS Cluster module 

│   ├── aws-vpc/ # VPC module 

│   ├── aws-ecr/ # ECR module 

│   ├── aws-rds/ # RDS module 

├── .terraform/ # Terraform working directory (generated when initialized) 

├── .terraform.lock.hcl # Provider version lock file(generated when initalized) 

### **Main Terraform Files:**

- **`main.tf`**: Contains core resource configurations like creating EKS, ECR and other infrastructure component modules.
  
- **`provider.tf`**: Defines the provider configuration for AWS. It includes details like authentication credentials(passed through pipeline), region, and provider-specific settings, if any.

- **`variables.tf`**: Contains the definitions for variables that will be used throughout the Terraform configurations. You can set default values or prompt for values when applying the Terraform plan.

- **`outputs.tf`**: Used to define outputs after the infrastructure is provisioned. Outputs can include resource IDs, IP addresses, or other valuable information.

- **`terraform.tfvars`**: Contains values for the variables defined in `variables.tf`. This is where we define environment-specific configurations, such as the AWS region or instance type. Currently we are overriding this file using pipeline.

- **`backend.tf`**: If you are using a remote backend (AWS S3), this file stores the configuration for managing Terraform's state remotely.

---

### **Modules Directory:**

The **`modules/`** directory contains reusable Terraform modules that are self-contained and can be referenced throughout your project.

Example structure:


## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_aws"></a> [aws](#requirement\_aws) | ~> 5.41.0 |
| <a name="requirement_helm"></a> [helm](#requirement\_helm) | 3.0.0-pre1 |
| <a name="requirement_kubernetes"></a> [kubernetes](#requirement\_kubernetes) | 2.35.1 |

## Providers

No providers.

## Modules

| Name | Source | Version |
|------|--------|---------|
| <a name="module_ecr_repositories"></a> [ecr\_repositories](#module\_ecr\_repositories) | ./modules/aws-ecr | n/a |
| <a name="module_eks_cluster"></a> [eks\_cluster](#module\_eks\_cluster) | ./modules/aws-eks | n/a |
| <a name="module_rds_cluster"></a> [rds\_cluster](#module\_rds\_cluster) | ./modules/aws-rds | n/a |
| <a name="module_vpc"></a> [vpc](#module\_vpc) | ./modules/aws-vpc | n/a |

## Resources

No resources.

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_availability_zones"></a> [availability\_zones](#input\_availability\_zones) | List of availability zones | `list(string)` | n/a | yes |
| <a name="input_aws_account_id"></a> [aws\_account\_id](#input\_aws\_account\_id) | AWS account id | `string` | n/a | yes |
| <a name="input_aws_profile"></a> [aws\_profile](#input\_aws\_profile) | AWS profile | `string` | `"aws"` | no |
| <a name="input_aws_region"></a> [aws\_region](#input\_aws\_region) | AWS region | `string` | `"us-east-1"` | no |
| <a name="input_backup_retention_period"></a> [backup\_retention\_period](#input\_backup\_retention\_period) | The backup retention period in days | `number` | `7` | no |
| <a name="input_ecr_repositories"></a> [ecr\_repositories](#input\_ecr\_repositories) | Repo List | `list(string)` | n/a | yes |
| <a name="input_env"></a> [env](#input\_env) | Deployment environment | `string` | n/a | yes |
| <a name="input_private_subnet_cidrs"></a> [private\_subnet\_cidrs](#input\_private\_subnet\_cidrs) | List of private subnets cidrs | `list(string)` | n/a | yes |
| <a name="input_project"></a> [project](#input\_project) | Deployment project | `string` | `"lscore"` | no |
| <a name="input_public_subnet_cidrs"></a> [public\_subnet\_cidrs](#input\_public\_subnet\_cidrs) | List of public subnets cidrs | `list(string)` | n/a | yes |
| <a name="input_rds_cluster_parameter_group_family"></a> [rds\_cluster\_parameter\_group\_family](#input\_rds\_cluster\_parameter\_group\_family) | Parameter group family of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_cluster_parameter_group_name"></a> [rds\_cluster\_parameter\_group\_name](#input\_rds\_cluster\_parameter\_group\_name) | Parameter group name of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_db_cluster_instance_class"></a> [rds\_db\_cluster\_instance\_class](#input\_rds\_db\_cluster\_instance\_class) | Instance class of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_db_name"></a> [rds\_db\_name](#input\_rds\_db\_name) | Name of RDS database | `string` | `""` | no |
| <a name="input_rds_engine"></a> [rds\_engine](#input\_rds\_engine) | Engine of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_engine_version"></a> [rds\_engine\_version](#input\_rds\_engine\_version) | Engine version of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_master_username"></a> [rds\_master\_username](#input\_rds\_master\_username) | Username of RDS master db | `string` | n/a | yes |
| <a name="input_vpc_cidr"></a> [vpc\_cidr](#input\_vpc\_cidr) | CIDR block of VPC | `string` | n/a | yes |

## Outputs

No outputs.

## Module Explanation 

## ECR 

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
| [aws_ecr_lifecycle_policy.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecr_lifecycle_policy) | resource |
| [aws_ecr_repository.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ecr_repository) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_ecr_repo_name"></a> [ecr\_repo\_name](#input\_ecr\_repo\_name) | Name of the ECR repository | `string` | n/a | yes |
| <a name="input_env"></a> [env](#input\_env) | Environment Dev or UAT or Prod | `string` | n/a | yes |
| <a name="input_project"></a> [project](#input\_project) | Name of the project | `string` | `"lscore"` | no |

## Outputs

No outputs.


## EKS

## Requirements

No requirements.

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | n/a |
| <a name="provider_helm"></a> [helm](#provider\_helm) | n/a |
| <a name="provider_kubernetes"></a> [kubernetes](#provider\_kubernetes) | n/a |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [aws_eks_cluster.eks](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eks_cluster) | resource |
| [aws_eks_node_group.node-ec2](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eks_node_group) | resource |
| [aws_iam_instance_profile.eks_worker_instance_profile](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_instance_profile) | resource |
| [aws_iam_openid_connect_provider.oidc_provider](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_openid_connect_provider) | resource |
| [aws_iam_policy.load_balancer_controller_policy](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_policy) | resource |
| [aws_iam_policy_attachment.eks_cluster_policy](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_policy_attachment) | resource |
| [aws_iam_policy_attachment.eks_node_policy](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_policy_attachment) | resource |
| [aws_iam_policy_attachment.load_balancer_controller_attachment](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_policy_attachment) | resource |
| [aws_iam_role.aws_load_balancer_controller_role](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_iam_role.eks_cluster_role](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_iam_role.eks_node_role](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/iam_role) | resource |
| [aws_security_group.eks_worker_sg](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group) | resource |
| [helm_release.aws_load_balancer_controller](https://registry.terraform.io/providers/hashicorp/helm/latest/docs/resources/release) | resource |
| [helm_release.csi_secrets_store](https://registry.terraform.io/providers/hashicorp/helm/latest/docs/resources/release) | resource |
| [helm_release.secrets_provider_aws](https://registry.terraform.io/providers/hashicorp/helm/latest/docs/resources/release) | resource |
| [kubernetes_manifest.aws_secrets_manager_secret_provider](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs/resources/manifest) | resource |
| [kubernetes_service_account.aws_load_balancer_controller_sa](https://registry.terraform.io/providers/hashicorp/kubernetes/latest/docs/resources/service_account) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_env"></a> [env](#input\_env) | Environment | `string` | n/a | yes |
| <a name="input_private_subnet"></a> [private\_subnet](#input\_private\_subnet) | List of private subnets | `list(string)` | n/a | yes |
| <a name="input_project"></a> [project](#input\_project) | lscore | `string` | `"lscore"` | no |
| <a name="input_public_subnet"></a> [public\_subnet](#input\_public\_subnet) | List of public subnets | `list(string)` | n/a | yes |
| <a name="input_region"></a> [region](#input\_region) | The AWS region to create resources | `string` | `"us-west-2"` | no |
| <a name="input_vpc_cidr"></a> [vpc\_cidr](#input\_vpc\_cidr) | The CIDR block for the VPC | `string` | `"10.0.0.0/16"` | no |
| <a name="input_vpc_id"></a> [vpc\_id](#input\_vpc\_id) | VPC id | `string` | n/a | yes |

## Outputs

No outputs.


## RDS

## Requirements

No requirements.

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | n/a |
| <a name="provider_random"></a> [random](#provider\_random) | n/a |

## Modules

No modules.

## Resources

| Name | Type |
|------|------|
| [aws_db_subnet_group.db_subnet_group](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/db_subnet_group) | resource |
| [aws_rds_cluster.rds_cluster](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/rds_cluster) | resource |
| [aws_rds_cluster_instance.rds_cluster_instance](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/rds_cluster_instance) | resource |
| [aws_rds_cluster_parameter_group.rds_cluster_parameter_group](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/rds_cluster_parameter_group) | resource |
| [aws_security_group.rds_security_group](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/security_group) | resource |
| [aws_vpc_security_group_egress_rule.allow_all_traffic](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc_security_group_egress_rule) | resource |
| [aws_vpc_security_group_ingress_rule.allow_vpc_traffic](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc_security_group_ingress_rule) | resource |
| [random_password.password](https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/password) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_backup_retention_period"></a> [backup\_retention\_period](#input\_backup\_retention\_period) | The backup retention period in days | `number` | `7` | no |
| <a name="input_cloudwatch_logs_exports"></a> [cloudwatch\_logs\_exports](#input\_cloudwatch\_logs\_exports) | The list of log types to export to CloudWatch | `list(string)` | <pre>[<br>  "postgresql"<br>]</pre> | no |
| <a name="input_db_subnet_group_name"></a> [db\_subnet\_group\_name](#input\_db\_subnet\_group\_name) | Subnet group name of DB | `string` | n/a | yes |
| <a name="input_db_subnet_ids"></a> [db\_subnet\_ids](#input\_db\_subnet\_ids) | Subnet IDs of DB | `list(string)` | n/a | yes |
| <a name="input_delete_automated_backups"></a> [delete\_automated\_backups](#input\_delete\_automated\_backups) | Whether to delete automated backups when the cluster is deleted | `bool` | `false` | no |
| <a name="input_deletion_protection"></a> [deletion\_protection](#input\_deletion\_protection) | Whether to enable deletion protection | `bool` | `false` | no |
| <a name="input_env"></a> [env](#input\_env) | Environment | `string` | n/a | yes |
| <a name="input_preferred_backup_window"></a> [preferred\_backup\_window](#input\_preferred\_backup\_window) | The preferred backup window for the RDS cluster | `string` | `"07:00-09:00"` | no |
| <a name="input_preferred_maintenance_window"></a> [preferred\_maintenance\_window](#input\_preferred\_maintenance\_window) | The preferred maintenance window for the RDS cluster | `string` | `"sun:11:00-sun:13:00"` | no |
| <a name="input_project"></a> [project](#input\_project) | lscore | `string` | `"lscore"` | no |
| <a name="input_rds_azs"></a> [rds\_azs](#input\_rds\_azs) | Availability zones of RDS cluster | `list(string)` | n/a | yes |
| <a name="input_rds_cluster_parameter_group_family"></a> [rds\_cluster\_parameter\_group\_family](#input\_rds\_cluster\_parameter\_group\_family) | Parameter group family of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_cluster_parameter_group_name"></a> [rds\_cluster\_parameter\_group\_name](#input\_rds\_cluster\_parameter\_group\_name) | Parameter group name of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_db_cluster_instance_class"></a> [rds\_db\_cluster\_instance\_class](#input\_rds\_db\_cluster\_instance\_class) | Instance class of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_db_name"></a> [rds\_db\_name](#input\_rds\_db\_name) | Name of RDS database | `string` | `""` | no |
| <a name="input_rds_engine"></a> [rds\_engine](#input\_rds\_engine) | Engine of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_engine_version"></a> [rds\_engine\_version](#input\_rds\_engine\_version) | Engine version of RDS cluster | `string` | n/a | yes |
| <a name="input_rds_master_username"></a> [rds\_master\_username](#input\_rds\_master\_username) | Username of RDS master db | `string` | n/a | yes |
| <a name="input_skip_final_snapshot"></a> [skip\_final\_snapshot](#input\_skip\_final\_snapshot) | Whether to skip taking a final snapshot before deletion | `bool` | `true` | no |
| <a name="input_vpc_cidr_block"></a> [vpc\_cidr\_block](#input\_vpc\_cidr\_block) | CIDR block of VPC | `string` | n/a | yes |
| <a name="input_vpc_id"></a> [vpc\_id](#input\_vpc\_id) | ID of VPC | `string` | n/a | yes |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_rds_endpoint"></a> [rds\_endpoint](#output\_rds\_endpoint) | n/a |
| <a name="output_rds_master_password"></a> [rds\_master\_password](#output\_rds\_master\_password) | n/a |
| <a name="output_rds_master_username"></a> [rds\_master\_username](#output\_rds\_master\_username) | n/a |


## VPC

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
| [aws_eip.nat](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eip) | resource |
| [aws_internet_gateway.main](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/internet_gateway) | resource |
| [aws_nat_gateway.main](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/nat_gateway) | resource |
| [aws_route.private](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route) | resource |
| [aws_route.public](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route) | resource |
| [aws_route_table.private](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route_table) | resource |
| [aws_route_table.public](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route_table) | resource |
| [aws_route_table_association.private_association](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route_table_association) | resource |
| [aws_route_table_association.public_association](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route_table_association) | resource |
| [aws_subnet.private](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/subnet) | resource |
| [aws_subnet.public](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/subnet) | resource |
| [aws_vpc.main](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_availability_zones"></a> [availability\_zones](#input\_availability\_zones) | Availability Zones for the subnets | `list(string)` | <pre>[<br>  "us-east-1a",<br>  "us-east-1b",<br>  "us-east-1c"<br>]</pre> | no |
| <a name="input_env"></a> [env](#input\_env) | n/a | `string` | `"dev"` | no |
| <a name="input_private_subnet_cidrs"></a> [private\_subnet\_cidrs](#input\_private\_subnet\_cidrs) | CIDR blocks for the private subnets | `list(string)` | <pre>[<br>  "10.0.4.0/24",<br>  "10.0.5.0/24",<br>  "10.0.6.0/24"<br>]</pre> | no |
| <a name="input_project"></a> [project](#input\_project) | n/a | `string` | `"lscore"` | no |
| <a name="input_public_subnet_cidrs"></a> [public\_subnet\_cidrs](#input\_public\_subnet\_cidrs) | CIDR blocks for the public subnets | `list(string)` | <pre>[<br>  "10.0.1.0/24",<br>  "10.0.2.0/24",<br>  "10.0.3.0/24"<br>]</pre> | no |
| <a name="input_vpc_cidr"></a> [vpc\_cidr](#input\_vpc\_cidr) | CIDR block for the VPC | `string` | `"10.0.0.0/16"` | no |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_private_subnet_ids"></a> [private\_subnet\_ids](#output\_private\_subnet\_ids) | n/a |
| <a name="output_public_subnet_ids"></a> [public\_subnet\_ids](#output\_public\_subnet\_ids) | n/a |
| <a name="output_vpc_id"></a> [vpc\_id](#output\_vpc\_id) | n/a |
