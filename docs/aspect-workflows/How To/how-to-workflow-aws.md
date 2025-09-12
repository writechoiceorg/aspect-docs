---
title: "How to deploy Aspect Workflows on AWS"
description: "Deploy Aspect Workflows on Amazon Web Services to optimize your CI/CD pipeline."
slug: /guides/deploy-workflows-on-aws
audience: "DevOps Engineer, Cloud Architect, or developer with intermediate experience in AWS, Terraform, or OpenTofu."
---

# Deploy Aspect Workflows on AWS

This guide explains how to deploy Aspect Workflows on Amazon Web Services (AWS) using Terraform or OpenTofu. Follow these steps to set up the cloud infrastructure and run your continuous integration/continuous delivery (CI/CD) pipelines more efficiently.

## Prerequisites

Before you begin, ensure you have the following tools installed and configured:

* **Terraform** (version >= 1.9) or **OpenTofu** (version >= 1.9): These tools manage infrastructure as code.
* **AWS Provider** (version >= 4.67.1): The Terraform provider for managing AWS resources.
* **AWS CLI**: The command-line interface for Amazon Web Services.

:::info
If you are new to Terraform or OpenTofu, run `terraform init` or `tofu init` before you begin.
:::

## Steps

### 1. Increase AWS quotas

To support Aspect Workflows, you must increase specific AWS service quotas in your account.

* Increase the **Concurrent executions** quota to at least 1,000. This allows Workflows Lambda functions to manage runner group scaling.
* If you are in the `us-east-1` region, you must also increase the **Managed policies per role** quota to a maximum of 20.

### 2. Configure Terraform

Next, configure your Terraform setup to use the Aspect Workflows module.

1.  If this is a new AWS account, perform a targeted apply of the VPC resources by running `terraform apply -target=module.vpc -target=module.vpc_endpoints`.
2.  Add the Aspect Workflows Terraform module to your `main.tf` file. You must specify the name and region of the S3 bucket where the module is located. For example, if the bucket is named `aspect-bucket-us-east-2` and is in the `us-east-2` region, your `main.tf` file should include the following code:

    ```tf
    backend "s3" {
      bucket = "test-bucket-us-east-2"
      key    = "global/s3/terraform.tfstate"
      region = "us-east-2"
    }
    ```

### 3. Deploy Workflows

After configuring the Terraform module, deploy the infrastructure to your AWS account.

* Run `terraform apply` to create the infrastructure.

## What's next

Once your AWS infrastructure is configured, you can integrate it with your chosen CI provider. For more information, see the documentation for your provider:

* [Buildkite](#)
* [CircleCI](#)
* [GitHub Actions](#)
* [GitLab](#)