# Pulumi GitHub Actions

Pulumi's GitHub Actions deploy apps and infrastructure to your cloud of choice, using just your favorite language
and GitHub. This includes previewing, validating, and collaborating on proposed deployments in the context of Pull
Requests, and triggering deployments or promotions between different environments by merging or directly committing code.

**Note**: This repository contains samples and additional documentation for using Pulumi's [Github Actions Docker container](https://hub.docker.com/r/pulumi/actions).
If you're looking for the code that builds that container, [you'll find it
here](https://github.com/pulumi/pulumi/tree/master/docker/actions).

## Getting Started

To get started with Pulumi's GitHub Actions, [check out our documentation](https://www.pulumi.com/docs/console/continuous-delivery/github-actions/).

## Demos and Examples

To see some examples of this in action, see the following links:

* [Our introductory blog post](https://blog.pulumi.com/continuous-delivery-to-any-cloud-using-github-actions-and-pulumi)
* [Dockerized Ruby on Rails, in Kubernetes, with hosted Cloud SQL](https://github.com/pulumi/actions-example-gke-rails)
* [Short 90 second video from GitHub Universe Keynote](https://www.youtube.com/watch?v=59SxB2uY9E0)
* [Short 90 second video on GitOps and Pull Request workflows](https://www.youtube.com/watch?v=MKbDVDBuKUA)
* [Longer 7 minute video exploring the ins and outs of Pulumi GitHub Actions in practice](https://www.youtube.com/watch?v=1Et2TkuxqJg)

## Cloud Providers

Below are some quick tips on using Pulumi's GitHub Actions support with your cloud provider. This typically
entails configuring a service principal for unattended access, storing the resulting credentials using
[GitHub Secrets](https://developer.github.com/actions/creating-workflows/storing-secrets/), and consuming
them using [the `secrets` attribute](
https://developer.github.com/actions/creating-workflows/workflow-configuration-options/#actions-attributes)
on your workflow's action.

> If your cloud of choice isn't listed, that doesn't necessarily mean Pulumi doesn't support it; please see
> [Pulumi's QuickStart page](https://pulumi.io/quickstart) for more complete documentation.

### Amazon Web Services (AWS)

For AWS, you'll need to create or use or use an existing IAM user for your action. Please see
[the Pulumi documentation page](https://pulumi.io/quickstart/aws/setup.html#environment-variables) for pointers
to the relevant AWS documentation for doing this.

As soon as you have an AWS user in hand, you'll set the environment variables `AWS_ACCESS_KEY_ID` and
`AWS_SECRET_ACCESS_KEY` using GitHub Secrets, and then consume them in your action:

```yaml
name: Pulumi
on:
  push:
    branches:
      - master
jobs:
  up:
    name: Update
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - uses: docker://pulumi/actions
        with:
          args: up
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          PULUMI_ACCESS_TOKEN: ${{ secrets.PULUMI_ACCESS_TOKEN }}
          PULUMI_CI: up
```

Failure to configure this correctly will lead to an error message.

### Microsoft Azure

For Azure, you'll need to create or use an existing Azure Service Principal for your action. Please see
[the Pulumi documentation page](https://pulumi.io/quickstart/azure/setup.html#service-principal-authentication) for
pointers to the relevant Azure documentation for doing this.

As soon as you have a service principal in hand, you'll set the environment variables `ARM_SUBSCRIPTION_ID`,
`ARM_CLIENT_ID`, `ARM_CLIENT_SECRET`, and `ARM_TENANT_ID` using GitHub Secrets, and consume them in your action:

```yaml
name: Pulumi
on:
  push:
    branches:
      - master
jobs:
  up:
    name: Update
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - uses: docker://pulumi/actions
        with:
          args: up
        env:
          ARM_SUBSCRIPTION_ID: ${{ secrets.ARM_SUBSCRIPTION_ID }}
          ARM_CLIENT_ID: ${{ secrets.ARM_CLIENT_ID }}
          ARM_CLIENT_SECRET: ${{ secrets.ARM_CLIENT_SECRET }}
          ARM_TENANT_ID: ${{ secrets.ARM_TENANT_ID }}
          PULUMI_ACCESS_TOKEN: ${{ secrets.PULUMI_ACCESS_TOKEN }}
          PULUMI_CI: up
```

Failure to configure this correctly will lead to the error message `Error building AzureRM Client: Azure CLI
Authorization Profile was not found. Please ensure the Azure CLI is installed and then log-in with 'az login'`.

### Google Cloud Platform

For GCP, you'll need to create or use or use an existing service account key. Please see
[the Pulumi documentation page](https://pulumi.io/quickstart/gcp/setup.html) for pointers
to the relevant GCP documentation for doing this.

As soon as you have credentials in hand, you'll set the environment variable `GOOGLE_CREDENTIALS` to contain the
credentials JSON using GitHub Secrets, and then consume it in your action:

```yaml
name: Pulumi
on:
  push:
    branches:
      - master
jobs:
  up:
    name: Update
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - uses: docker://pulumi/actions
        with:
          args: up
        env:
          GOOGLE_CREDENTIALS: ${{ secrets.GOOGLE_CREDENTIALS }}
          PULUMI_ACCESS_TOKEN: ${{ secrets.PULUMI_ACCESS_TOKEN }}
          PULUMI_CI: up
```

Failure to configure this correctly will lead to an error message.
