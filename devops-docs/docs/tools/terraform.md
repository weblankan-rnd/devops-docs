# Terraform

## Overview

Terraform is an Infrastructure as Code tool for provisioning and managing cloud infrastructure.

## Key Concepts

**Resource**: AWS/Azure/GCP infrastructure component

**Provider**: Cloud platform plugin

**Module**: Reusable infrastructure code

**Variable**: Input parameter

**Output**: Export value

## Basic Configuration

```hcl
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}

resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  
  tags = {
    Name = "web-server"
  }
}

variable "aws_region" {
  description = "AWS region"
  default     = "us-east-1"
}

output "instance_ip" {
  value = aws_instance.web.public_ip
}
```

## Common Commands

```bash
# Initialize working directory
terraform init

# Validate configuration
terraform validate

# Format code
terraform fmt -recursive

# Show execution plan
terraform plan

# Apply configuration
terraform apply

# Destroy resources
terraform destroy

# Show state
terraform show

# Import existing resource
terraform import aws_instance.example i-1234567890abcdef0
```

## Best Practices

1. **State management** - Use remote backend
2. **Variable files** - Separate configuration
3. **Modules** - Organize code
4. **Workspaces** - Manage environments
5. **Version control** - Track changes
6. **Plan before apply** - Review changes

## State Management

```
Backends:
- S3 - AWS
- Azure Blob Storage - Azure
- GCS - Google Cloud
- Terraform Cloud - Remote state
```

## Modules Example

```hcl
module "vpc" {
  source = "./modules/vpc"
  
  cidr_block = "10.0.0.0/16"
  environment = var.environment
}

module "web_servers" {
  source = "./modules/web-servers"
  
  vpc_id          = module.vpc.vpc_id
  subnet_ids      = module.vpc.subnet_ids
  instance_count  = var.instance_count
}
```

## Workspaces

```bash
# Create environment
terraform workspace new production

# List workspaces
terraform workspace list

# Select workspace
terraform workspace select production

# Apply to specific workspace
terraform apply
```

