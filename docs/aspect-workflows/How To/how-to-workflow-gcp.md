---
title: "Deploy Aspect Workflows on GCP"
description: "This guide shows you how to deploy Aspect Workflows on Google Cloud Platform (GCP)."
slug: /get-started/deploy-workflows/deploy-gcp
audience: "Developer, DevOps Engineer, or Cloud Architect with basic knowledge of cloud services."
---

## Prerequisites

Before you begin, ensure you have the following tools installed and APIs enabled.

### Tool installation

* Terraform CLI or OpenTofu CLI (version >= 1.4.0)
    * [Installation instructions](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
    * [OpenTofu installation instructions](https://docs.opentofu.io/getting-started/install/)
* A GCP account with:
    * [Google Cloud SDK](https://cloud.google.com/sdk/docs/install), `gcloud`, installed and initialized.
    * A [Google Cloud project](https://cloud.google.com/resource-manager/docs/creating-managing-projects)
    * A [Google Cloud Storage bucket](https://cloud.google.com/storage/docs/creating-buckets) to use as a backend.

<Note>
If you are new to Terraform or OpenTofu, run `terraform init` or `tofu init` before you begin.
</Note>

### Enable required services

You'll have to enable the following APIs in your project:
  * [Cloud Build API](https://console.cloud.google.com/apis/library/cloudbuild.googleapis.com)
  * [Cloud Functions API](https://console.cloud.google.com/apis/library/cloudfunctions.googleapis.com)
  * [Cloud Resource Manager API](https://console.cloud.google.com/marketplace/product/google/cloudresourcemanager.googleapis.com)
  * [Cloud Run Admin API](https://console.cloud.google.com/apis/library/run.googleapis.com)
  * [Cloud Scheduler API](https://console.cloud.google.com/apis/library/cloudscheduler.googleapis.com)
  * [Compute Engine API](https://console.cloud.google.com/apis/library/compute.googleapis.com)
  * [Google Cloud Memorystore for Redis API](https://console.cloud.google.com/apis/library/redis.googleapis.com)
  * [Identity and Access Management (IAM) API](https://console.cloud.google.com/marketplace/product/google/iam.googleapis.com)
  * [Kubernetes Engine API](https://console.cloud.google.com/marketplace/product/google/container.googleapis.com)
  * [Secret Manager API](https://console.cloud.google.com/marketplace/product/google/secretmanager.googleapis.com)
  * [Service Networking API](https://console.cloud.google.com/apis/library/servicenetworking.googleapis.com)
  * [Storage Transfer API](https://console.cloud.google.com/apis/library/storagetransfer.googleapis.com)
  * [Workflows API](https://console.cloud.google.com/apis/library/workflows.googleapis.com)

<Note>
Aspect Workflows uses the Storage Transfer API to copy bootstrap scripts and other files required by Workflows.
</Note>

If you plan to enable the external remote endpoint or Web UI, you also need to enable the following APIs:
  * [API Keys API](https://console.cloud.google.com/apis/api/apikeys.googleapis.com)
  * [Cloud DNS API](https://console.cloud.google.com/marketplace/product/google/dns.googleapis.com)
  * [Cloud Identity-Aware Proxy API](https://console.cloud.google.com/apis/library/iap.googleapis.com)
  * [Cloud SQL Admin API](https://console.cloud.google.com/apis/library/sqladmin.googleapis.com)
  * [Identity Platform](https://console.cloud.google.com/marketplace/details/google-cloud-platform/customer-identity)
  * [Identity Toolkit API](https://console.cloud.google.com/apis/library/identitytoolkit.googleapis.com)

You may need to wait a short while for the changes to propagate.

## Steps

### 1. Identify your Project ID

A common mistake is confusing the **Project Number** with the **Project ID**. Terraform needs the **Project ID**, a unique string (for example, `my-project-410113`).

If you only have the **Project Number** (for example, `8936433341363`), use the following `gcloud` command to find the ID:

```bash
gcloud projects describe 8936433341363 --format="value(projectId)"
```

Save this **Project ID**; you'll need it for your Terraform file.

### 2. Authenticate your local environment

For Terraform to manage your GCP resources, it needs credentials. The simplest way to authenticate on your local machine is with `gcloud`.

Run the following command. It opens a browser window for you to log in and authorize access.

```bash
gcloud auth application-default login
```

### 3. Terraform ans service account permissions

You'll also need to grant the service account permissions to Terraform to manage your GCP resources.
To grant permissions, run the following command:

```bash
gcloud projects add-iam-policy-binding YOUR-PROJECT-ID --member serviceAccount:YOUR-SERVICE-ACCOUNT-EMAIL --role roles/owner
```

You'll also need to grant yourself a role that allows creating custom roles.

```bash
gcloud projects add-iam-policy-binding <PROJECT_ID> --member=user:<YOUR_EMAIL> --role=roles/iam.roleAdmin
```

### 4. Configure the `main.tf` file

The `main.tf` file contains your Terraform configuration, including the backend (where your infrastructure state is saved) and the provider (which interacts with GCP).

Copy the code below into `main.tf`. **Remember to change the values** in the `locals` and the `terraform` blocks to match your project.

```tf
terraform {
  required_version = ">= 1.4.0"
  backend "gcs" {
    bucket = "YOUR-BUCKET-NAME"
    prefix = "terraform/state"
  }
}

locals {
  project = "YOUR-PROJECT-NUMBER"
  project_id = "YOUR-PROJECT-ID"
  region  = "YOUR-PROJECT-REGION"
  zones = [
    "${local.region}-a",
    "${local.region}-b",
    "${local.region}-c",
  ]
}

(...)
```

### 5. Configure CI/CD

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

After configuring the Terraform module, deploy the infrastructure to your GCP account.

* Run `terraform apply` to create the infrastructure.

**Key Points in the Code:**

  * **`backend "gcs"`**: Tells Terraform to save the state file (`terraform.tfstate`) in the bucket instead of locally.
  * **`locals`**: Helps avoid repeating values. You can define the `project_id` and `region` here.
  * **`provider "google"`**: This is the plugin that enables Terraform to communicate with the Google API. It's configured to use the `project_id` and `region` you defined.

-----

## Troubleshooting

  * **Project ID vs. Project Number**: Remember to use the Project ID string, not the numerical Project Number, in your Terraform files.
  * **State file management**: Always use a remote backend like a GCS bucket to avoid losing your state file or having security leaks.
  * **Authentication**: If you're having permission issues, verify that your IAM user or role has the necessary permissions to perform the actions specified in your code.

-----

## See also

  * **Google Cloud SDK**: [https://cloud.google.com/sdk/](https://cloud.google.com/sdk/)
  * **Terraform**: [https://developer.hashicorp.com/terraform/](https://developer.hashicorp.com/terraform/)
  * **Google Cloud Storage**: [https://cloud.google.com/storage](https://cloud.google.com/storage)