# Terraform EC2 HA

Zero Downtime and Highly Available deployment infrastructure using Terraform and EC2.

## Basic Usage

```terraform
/* -----------------------------------------------------------
 *  Configure Gambit High Available Infrastructure
 * ----------------------------------------------------------- */
module "aws-ec2-ha" {
  source = "git@github.com:miere/terraform-ha-ec2.git"

  aws_region                  = "${var.aws_region}"
  aws_vpc_id                  = "${var.aws_vpc_id}"
  aws_instances_subnet_ids    = "${data.aws_subnet_ids.private.ids}"

  aws_lb_subnet_ids    = "${data.aws_subnet_ids.public.ids}"
  aws_lb_health_check_url  = "/health"
  aws_lb_health_check_type = "ELB"
  aws_lb_health_check_grace_period = "60"

  environment       = "${var.environment}"
}
```

## Inputs

| Name                             | Description                                                                                            | Type   | Default                          | Required |
| -------------------------------- | ------------------------------------------------------------------------------------------------------ | ------ | -------------------------------- | -------- |
| aws_asg_instances_desired        | Desired number of instances on the ASG.                                                                | string | `"2"`                            | no       |
| aws_asg_instances_max            | Maximum number of instances on the ASG.                                                                | string | `"3"`                            | no       |
| aws_asg_instances_min            | Minimum number of instances on the ASG.                                                                | string | `"2"`                            | no       |
| aws_deployment_config            | AWS CodeDeploy deployment config. Default: CodeDeployDefault.OneAtATime                                | string | `"CodeDeployDefault.OneAtATime"` | no       |
| aws_deployment_group             | AWS CodeDeploy deployment group. Default: default                                                      | string | `"default"`                      | no       |
| aws_hosted_domain                | AWS Route 53 hosted zone domain. e.g. my.domain.com                                                    | string | n/a                              | yes      |
| aws_instance_type                | AWS EC2 instance type that will be used to spin up the service. Default: t3.nano                       | string | `"t3.nano"`                      | no       |
| aws_instance_web_port            | Define which port should be mapped from the ALB to target instance.                                    | string | `"8080"`                         | no       |
| aws_instance_web_protocol        | Define which protocol should be mapped from the ALB to target instance.                                | string | `"HTTP"`                         | no       |
| aws_instances_subnet_ids         | AWS Subnet IDs in which your instances will be placed on.                                              | list   | n/a                              | yes      |
| aws_lb_deregistration_delay      | The delay expected during the draining phase to leverage a graceful shutdown for deregistered machines | string | `"60"`                           | no       |
| aws_lb_health_check_grace_period | Grace period before the instance being checked.                                                        | string | `"30"`                           | no       |
| aws_lb_health_check_type         | Define how AWS should check if instances are healthy or not.                                           | string | `"ELB"`                          | no       |
| aws_lb_health_check_url          | Define which URL ALB should probe to ensure the instances are healthy.                                 | string | `"/health-check"`                | no       |
| aws_lb_subnet_ids                | AWS Subnet IDs in which your load balancer will be placed on.                                          | list   | n/a                              | yes      |
| aws_region                       | AWS region in which the artifact will be deployed to. It will be used by S3 and Code Deploy.           | string | n/a                              | yes      |
| aws_vpc_id                       | AWS VPC ID in which your services will be deployed to.                                                 | string | n/a                              | yes      |
| custom_script                    | The custom script path. This script, if defined, will be run every time an instance is spin up.        | string | `"-"`                            | no       |
| environment                      | An environment deployment identifier.                                                                  | string | `""`                             | no       |
| name                             | Optional name that will be used as suffix for every AWS resource created by this script.               | string | `""`                             | no       |

## Outputs

| Name               | Description                                              |
| ------------------ | -------------------------------------------------------- |
| aws_ami_name       | The AMI used by this deployment                          |
| aws_asg_arn        | The Auto Scaling Group arn created for this application  |
| aws_iam_role_arn   | The IAM Role ARN created for this application            |
| aws_iam_role_name  | The IAM Role Name created for this application           |
| aws_route53_record | The Route53 record that was created for this application |
