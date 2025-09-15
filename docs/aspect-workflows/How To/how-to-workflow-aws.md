---
title: "Deploy Aspect Workflows on AWS"
description: "Deploy Aspect Workflows on Amazon Web Services."
slug: /get-started/deploy-workflows/deploy-aws
audience: "DevOps Engineer, Cloud Architect, or developer with intermediate experience in AWS, Terraform, or OpenTofu."
---

## Prerequisites

Before you begin, ensure you have the following tools installed and configured:

* Terraform CLI or OpenTofu CLI (version >= 1.4.0)
    * [Installation instructions](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
    * [OpenTofu installation instructions](https://docs.opentofu.io/getting-started/install/)
* [AWS CLI](https://aws.amazon.com/cli/?nc1=h_ls)
* An AWS account with:
  * An [S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html) to use as a backend for Terraform state.
  * An [IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) with programmatic access and the necessary permissions to create and manage AWS resources.
  * The AWS CLI configured with the credentials of the IAM user. You can configure it by running `aws configure` and providing the access key, secret key, region, and output format.

<Note>
If you are new to Terraform or OpenTofu, run `terraform init` or `tofu init` before you begin.
</Note>

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

### 3. Configure CI/CD

To configure CI/CD, you must add the Aspect Workflows Terraform module to your CI/CD pipeline. For this, you can use the following code on the `workflows.tf` file for any of the four CI/CD providers:

<CodeGroup>

```tf GitHub Actions
  hosts = ["gha"]

  # Github Actions runner groups
  # WORKFLOWS_TEMPLATE: Once the Aspect Workflows GitHub actions land in your repository, run the following command
  # using the GitHub CLI to determine the workflow ID for the `gha_workflow_ids` fields below:
  # gh api -H "Accept: application/vnd.github+json" -H "X-GitHub-Api-Version: 2022-11-28" /repos/<org>/<repo>/actions/workflows
  # See https://docs.github.com/en/rest/actions/workflows?apiVersion=2022-11-28#list-repository-workflows for more info.
  gha_runner_groups = {
    # The `default` runner group is used for general bazel tasks such as build & test.
    default = {
      agent_idle_timeout_min    = 120
      gh_repo                   = "YOUR_REPOSITORY_HERE" # `org/repo` of the GitHub repository under test
      gha_workflow_ids          = [] # WORKFLOWS_TEMPLATE: to reduce GitHub API call frequency and prevent rate limiting, add the workflow ID of the Aspect Workflows main GitHub Action
      max_runners               = 50
      min_runners               = 0
      queue                     = "aspect-default"
      resource_type             = "default"
      scaling_polling_frequency = 2 # check for new jobs every 30s
      warming                   = true
    }
    # The `small` runner group is used for the Setup Aspect Workflows pipeline step.
    # These are intentionally small, inexpensive, long-lived instances that are not
    # meant for running bazel tasks.
    small = {
      agent_idle_timeout_min    = 720
      gh_repo                   = "YOUR_REPOSITORY_HERE" # `org/repo` of the GitHub repository under test
      gha_workflow_ids          = [] # WORKFLOWS_TEMPLATE: to reduce GitHub API call frequency and prevent rate limiting, add the workflow ID of the Aspect Workflows main GitHub Action
      max_runners               = 4
      min_runners               = 0
      queue                     = "aspect-small"
      resource_type             = "small"
      scaling_polling_frequency = 2     # check for new jobs every 30s
      warming                   = false # no need to warm this runner since it doesn't run bazel
    }
    # The `warming` running group is used for the warming job to create warming
    # archives used by other runner groups to pre-warm external repositories
    # during bootstrap in order to speed up their first jobs.
    warming = {
      agent_idle_timeout_min = 1
      gh_repo                = "YOUR_REPOSITORY_HERE" # `org/repo` of the GitHub repository under test
      gha_workflow_ids       = [] # WORKFLOWS_TEMPLATE: to reduce GitHub API call frequency and prevent rate limiting, add the workflow ID of the Aspect Workflows warming GitHub Action
      max_runners            = 1
      min_runners            = 0
policies               = { warming_manage : module.aspect_workflows.warming_management_policies["default"].arn }
queue                  = "aspect-warming"
      resource_type          = "default"
      warming                = false # do not warm runners used to generate warming archives
    }
  }
```
```tf Buildkite
bk_runner_groups = {
    # The `default` runner group is used for general bazel tasks such as build & test.
    default = {
      agent_idle_timeout_min    = 120
      max_runners               = 50
      min_runners               = 0
      queue                     = "aspect-default"
      resource_type             = "default"
      scaling_polling_frequency = 2 # check for new jobs every 30s
      warming                   = true
      # WORKFLOWS_TEMPLATE: You can add tags to the resources from a specific runner group.
      # Note that workflows adds the following tag to all resources by default
      #     "created-by": "aspect-workflows"
      # tags = {
      #   "some-runner-tag-key": "some-runner-tag-value"
      # }
    }
    # The `small` runner group is used for the Setup Aspect Workflows pipeline step.
    # These are intentionally small, inexpensive, long-lived instances that are not
    # meant for running bazel tasks.
    small = {
      agent_idle_timeout_min    = 720
      max_runners               = 4
      min_runners               = 0
      queue                     = "aspect-small"
      resource_type             = "small"
      scaling_polling_frequency = 2     # check for new jobs every 30s
      warming                   = false # no need to warm this runner since it doesn't run bazel
    }
    # The `warming` running group is used for the warming job to create warming
    # archives used by other runner groups to pre-warm external repositories
    # during bootstrap in order to speed up their first jobs.
    warming = {
      agent_idle_timeout_min = 1
      max_runners            = 1
      min_runners            = 0
queue                  = "aspect-warming"
      resource_type          = "default"
      warming                = false # do not warm runners used to generate warming archives
    }
```
```tf CircleCI
# CircleCI runner groups
  cci_runner_groups = {
    # The `default` runner group is used for general bazel tasks such as build & test.
    default = {
      agent_idle_timeout_min    = 120
      max_runners               = 50
      min_runners               = 0
      resource_type             = "default"
      scaling_polling_frequency = 2 # check for new jobs every 30s
      warming                   = true
    }
    # The `warming` running group is used for the warming job to create warming
    # archives used by other runner groups to pre-warm external repositories
    # during bootstrap in order to speed up their first jobs.
    warming = {
      agent_idle_timeout_min = 1
      max_runners            = 1
      min_runners            = 0
resource_type          = "default"
      warming                = false # do not warm runners used to generate warming archives
    }
  }
```
```tf GitLab
 gl_runner_groups = {
    # The `default` runner group is used for general bazel tasks such as build & test.
    default = {
      agent_idle_timeout_min    = 120
      max_runners               = 50
      min_runners               = 0
      project_id                = "GITLAB_PROJECT_ID" # Project ID of the GitLab repository under test
      queue                     = "aspect-default"
      resource_type             = "default"
      scaling_polling_frequency = 2 # check for new jobs every 30s
      warming                   = true
    }
    # The `small` runner group is used for the Setup Aspect Workflows pipeline step.
    # These are intentionally small, inexpensive, long-lived instances that are not
    # meant for running bazel tasks.
    small = {
      agent_idle_timeout_min    = 720
      max_runners               = 4
      min_runners               = 0
      project_id                = "GITLAB_PROJECT_ID" # Project ID of the GitLab repository under test
      queue                     = "aspect-small"
      resource_type             = "small"
      scaling_polling_frequency = 2     # check for new jobs every 30s
      warming                   = false # no need to warm this runner since it doesn't run bazel
    }
    # The `warming` running group is used for the warming job to create warming
    # archives used by other runner groups to pre-warm external repositories
    # during bootstrap in order to speed up their first jobs.
    warming = {
      agent_idle_timeout_min = 1
      max_runners            = 1
      min_runners            = 0
      project_id             = "GITLAB_PROJECT_ID" # Project ID of the GitLab repository under test
queue                  = "aspect-warming"
      resource_type          = "default"
      warming                = false # do not warm runners used to generate warming archives
    }
  }
```
</CodeGroup>

<Note>
- For the GitHub Actions, you need to insert the repository in the org/repository format. For instance, `YourCompany/YourRepository`.
- For the GitLab, you need to insert the project ID of the GitLab repository under test.
- For the CircleCI, you need to insert the project ID of the CircleCI repository under test.
- For the Buildkite, you need to insert the project ID of the Buildkite repository under test.
</Note>

### 4. Deploy Workflows

After configuring the Terraform module, deploy the infrastructure to your AWS account.

* Run `terraform apply` to create the infrastructure.

## See also

  * **AWS CLI**: [https://aws.amazon.com/cli/](https://aws.amazon.com/cli/)
  * **Terraform**: [https://developer.hashicorp.com/terraform/](https://developer.hashicorp.com/terraform/)
  * **AWS S3**: [https://aws.amazon.com/s3/](https://aws.amazon.com/s3/)