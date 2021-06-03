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

## Module

- [registry page](https://registry.terraform.io/modules/jina-ai/jinad-aws/jina/latest)
- [source code](https://github.com/jina-ai/terraform-jina-jinad-aws)

### Usage
```hcl
module "jinad" {
   source         = "jina-ai/jinad-aws/jina"
   instances      = {
     encoder: {
       type: "c5.4xlarge"
       disk = {
         type = "gp2"
         size = 20
       }
       pip: [ "tensorflow>=2.0", "transformers>=2.6.0" ]
       command: "sudo apt install -y jq"
     }
     indexer: {
       type: "i3.2xlarge"
       disk = {
         type = "gp2"
         size = 20
       }
       pip: [ "faiss-cpu==1.6.5", "redis==3.5.3" ]
       command: "sudo apt-get install -y redis-server && sudo redis-server --bind 0.0.0.0 --port 6379:6379 --daemonize yes"
     }
   }
   availability_zone = "us-east-1a"
   vpc_cidr       = "34.121.0.0/24"
   subnet_cidr    = "34.121.0.0/28"
   additional_tags = {
     "my_tag_key" = "my_tag_value"
   }
}

output "jinad_ips" {
   description   = "IP of JinaD"
   value         = module.jinad.instance_ips
}
```

Store the outputs from `jinad_ips` & Use it with `jina`

```
from jina import Flow
f = (Flow()
     .add(uses='MyAwesomeEncoder',
          host=<jinad_ips.encoder>:8000),
     .add(uses='MyAwesomeIndexer',
          host=<jinad_ips.indexer>:8000))

with f:
   f.index(...)
```
### Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| additional\_tags | Additional resource tags | `map(string)` | `{}` | no |
| availability\_zone | Mention the availability\_zone where JinaD resources are going to get created | `string` | `"us-east-1a"` | no |
| instances | Describe instance configuration here. | `map(any)` | <pre>{<br>  "instance1": {<br>    "command": "sudo echo \"Hello from instance1\"",<br>    "disk": {<br>      "size": 50,<br>      "type": "gp2"<br>    },<br>    "pip": [<br>      "Pillow",<br>      "transformers"<br>    ],<br>    "type": "t2.micro"<br>  },<br>  "instance2": {<br>    "command": "sudo echo \"Hello from instance2\"",<br>    "disk": {<br>      "size": 20,<br>      "type": "gp2"<br>    },<br>    "pip": [<br>      "annoy"<br>    ],<br>    "type": "t2.micro"<br>  }<br>}</pre> | no |
| region | Mention the Region where JinaD resources are going to get created | `string` | `"us-east-1"` | no |
| subnet\_cidr | Mention the CIDR of the subnet | `string` | `"10.113.0.0/16"` | no |
| vpc\_cidr | Mention the CIDR of the VPC | `string` | `"10.113.0.0/16"` | no |

### Ouputs

| Name | Description |
|------|-------------|
| instance\_ips | Elastic IPs of JinaD instances created as a map |
| instance\_keys | Private key of JinaD instances for debugging |

## Practice

### Set Up

#### [Install Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started)

- To install Terraform, find the appropriate package for your system and download it as a zip archive.
- After downloading Terraform, unzip the package. Terraform runs as a single binary named `terraform`. Any other files in the package can be safely removed and Terraform will still function.
- Finally, make sure that the `terraform` binary is available on your `PATH`. This process will differ depending on your operating system.

#### [Configure AWS Credentials Locally](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-creds)

```bash
aws configure
AWS Access Key ID [None]: <YOUR_ACCESS_KEY_ID>
AWS Secret Access Key [None]: <YOUR_ACCESS_KEY>
Default region name [None]: <YOUR_REION>
Default output format [None]: json
```

### [Terraform CLI](https://www.terraform.io/docs/cli/index.html)

1. Basic 
   1. `terraform init` is used to initialize a working directory containing Terraform configuration files.
   2. `terraform apply` executes the actions proposed in a Terraform plan.
   3. `terraform output` extract the value of an output variable from the state file.
   4. `terraform destory` is a convenient way to destroy all remote objects managed by a particular Terraform configuration.
2. More
   1. `terraform validate` validates the configuration files in a directory, referring only to the configuration and not accessing any remote services such as remote state, provider APIs, etc.
   2. `terrafrom plan` creates an execution plan. By default, creating a plan consists
   3. `terraform console` provides an interactive console for evaluating expressions.
