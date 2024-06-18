# Cloud Architecture Defaults

The following standards outline the recommended configurations and practices for cloud-based services to ensure optimal security, scalability, and efficiency in line with industry best practices.

![Architecture](./architecture/assets/architecture.png)

## Table of Contents

The following topics are covered in this document:
- [**Cloud IAM**](./architecture/cloud_iam.md): Guidelines for managing Identity and Access Management (IAM) in google cloud.
- Secret Manager: Best practices for storing and managing secrets in Google Cloud Secret Manager.
  - [**AppEngine with Cloud Build Secret Manager**](architecture/gcp_secret_manager/secret_manager_appengine_cloud_build)
  - [**Cloud Run Secret Manager**](architecture/gcp_secret_manager/secret_manager_cloud_run.md)
- [**Github Actions**](./architecture/github_actions.md): Guidelines for automating, customizing, and executing your software development workflows right in your repository with GitHub Actions.
- [**Snyk**](./architecture/snyk.md): A developer security platform that integrates into development tools and workflows, making it easy to find, prioritize, and fix security vulnerabilities in code, dependencies, containers, and infrastructure as code.