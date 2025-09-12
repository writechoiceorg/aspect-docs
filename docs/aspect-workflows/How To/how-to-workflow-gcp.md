---
title: "How to configure your first GCP project with Terraform"
description: "Configure a Terraform project to manage resources in Google Cloud, from initial setup to running the first commands."
slug: {slug}
audience: "Developer, DevOps Engineer, or Cloud Architect with basic knowledge of cloud services."
---

# Configure your first GCP project with Terraform

This guide shows you how to configure a Terraform project to manage resources in Google Cloud (GCP). You'll learn how to find the correct project information, initialize Terraform with a remote backend, and fix common errors.

## Prerequisites

Before you begin, ensure you have the following tools installed and APIs enabled.

### Tool installation

  * **Terraform CLI:** [Installation instructions](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
  * **Google Cloud SDK (`gcloud`):** [Installation instructions](https://cloud.google.com/sdk/docs/install)
  * A Google Cloud project already created.
  * A Google Cloud Storage bucket to use as a backend.

### Enable required services

Enable the following APIs in your project:

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

:::note
Aspect Workflows uses the Storage Transfer API to copy bootstrap scripts and other files required by Workflows.
:::

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

### 3. Configure the `main.tf` file

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

provider "google" {
  project = local.project
  region  = local.region
}

provider "google-beta" {
  project = local.project
  region  = local.region
}

```

**Key Points in the Code:**

  * **`backend "gcs"`**: Tells Terraform to save the state file (`terraform.tfstate`) in the bucket instead of locally.
  * **`locals`**: Helps avoid repeating values. You can define the `project_id` and `region` here.
  * **`provider "google"`**: This is the plugin that enables Terraform to communicate with the Google API. It's configured to use the `project_id` and `region` you defined.

-----

## 4. Run the Terraform workflow

With the `main.tf` file saved, open your terminal in the same folder and run these three essential commands:

**1. Initialize the Project:**

The `init` command prepares your environment. It downloads the providers (`google`, `google-beta`) and connects to your GCS backend bucket.

```bash
terraform init
```

**2. Plan the Changes:**

The `plan` command provides a preview of your infrastructure changes. It shows what will be created, changed, or destroyed in the cloud. This is a crucial safety step to prevent mistakes.

```bash
terraform plan
```

*(Since you haven't defined any resources yet, the plan will say "No changes. Infrastructure is up-to-date.")*

**3. Apply the Changes:**

The `apply` command executes the plan. It asks for a final confirmation (`yes`) before making any changes to your infrastructure.

```bash
terraform apply
```

-----

## Troubleshooting

  * **Project ID vs. Project Number**: Remember to use the Project ID string, not the numerical Project Number, in your Terraform files.
  * **State file management**: Always use a remote backend like a GCS bucket to avoid losing your state file or having security leaks.
  * **Skipping `plan`**: Always run `terraform plan` before `terraform apply` to review the changes and catch errors early.
  * **Authentication**: If you're having permission issues, verify that your IAM user or role has the necessary permissions to perform the actions specified in your code.

-----

## See also

  * **Google Cloud SDK**: [https://cloud.google.com/sdk/](https://cloud.google.com/sdk/)
  * **Terraform**: [https://developer.hashicorp.com/terraform/](https://developer.hashicorp.com/terraform/)
  * **Google Cloud Storage**: [https://cloud.google.com/storage](https://cloud.google.com/storage)