# Deploying JinaD on AWS with Terraform

## Overview

Here is a [terraform module jinad-aws](https://registry.terraform.io/modules/jina-ai/jinad-aws/jina/latest) to deploy `JinaD` on AWS by terraform.

With this terraform module you can:
- Custom the type and size of the root disk
- Deploy multiple AWS EC2 instance
- Execute extra command line
- Install `jinad`
- Manage `jinad` with `systemd`
- Add other python packages as required

## Concepts

- [Terraform](https://www.terraform.io/): Terraform is an open-source infrastructure as code software tool that provides a consistent CLI workflow to manage hundreds of cloud services. Terraform codifies cloud APIs into declarative configuration files.
- [Registry](https://registry.terraform.io/): The Terraform Registry is an interactive resource for discovering a wide selection of integrations (providers) and configuration packages (**modules**) for use with Terraform.
- [Modules](https://www.terraform.io/docs/registry/modules/use.html#using-modules) The Terraform Registry is integrated directly into Terraform, so a Terraform configuration can refer to any module published in the registry. The syntax for specifying a registry module is `<NAMESPACE>/<NAME>/<PROVIDER>`. For example: `hashicorp/consul/aws`.

## Practice

### Set Up

#### [Install Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started)

- To install Terraform, find the appropriate package for your system and download it as a zip archive.
- After downloading Terraform, unzip the package. Terraform runs as a single binary named `terraform`. Any other files in the package can be safely removed and Terraform will still function.
- Finally, make sure that the `terraform` binary is available on your `PATH`. This process will differ depending on your operating system.

#### [Configure AWS Credentials Locally](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-creds)

Configure credentials for local AWS CLI by `aws configure`:

```bash
aws configure
AWS Access Key ID [None]: *YOUR_ACCESS_KEY_ID*
AWS Secret Access Key [None]: *YOUR_ACCESS_KEY*
Default region name [None]: *YOUR_REION*
Default output format [None]: json
```

### Practice

1. `terraform init`
2. `terraform deploy`
3. use
4. debug
5. `terraform destory`
