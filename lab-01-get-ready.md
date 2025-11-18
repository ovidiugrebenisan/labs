# Lab 1: Get Ready with Kosli

## Learning Goals

- Create a Kosli account and understand the platform basics
- Fork and set up the sample application repository
- Verify that the CI/CD pipeline runs successfully
- Understand the basic structure of the application and its deployment process

## Introduction

Kosli is a DevOps platform that provides visibility and compliance for your software supply chain. It tracks changes throughout your development and deployment pipeline, recording what was built, tested, and deployed. Before diving into Kosli's features, you need to set up your account and verify that your sample application builds and deploys correctly.

This lab uses a simple Java Micronaut application with a complete CI/CD pipeline already configured in GitHub Actions. The pipeline builds the application, creates a Docker image, runs tests, and deploys it. In subsequent labs, you'll integrate Kosli to track all these activities.

## Prerequisites

- A GitHub account
- Basic familiarity with Git and GitHub
- Understanding of CI/CD concepts
- A web browser

## Exercise

### Overview

In this lab, you will:

- Create a free Kosli account
- Fork the kosli-tryout repository to your GitHub account
- Enable GitHub Actions on your forked repository
- Verify that the pipeline runs successfully and produces artifacts
- Observe the application deployment process

### Step-by-step instructions

<details>
<summary>More Details</summary>

#### Create a Kosli account

1. Navigate to [https://kosli.com](https://kosli.com)
2. Click on "Sign Up" or "Get Started"
3. Choose to sign up with GitHub (recommended) or email
4. Complete the registration process
5. Verify your email address if required
6. Log in to [https://app.kosli.com](https://app.kosli.com)
7. Create or join an organization when prompted
   - Organization name should be unique and memorable
   - This will be used in future labs to track your artifacts

> :bulb: Keep your organization name handy - you'll need it in later labs when configuring Kosli CLI commands.

#### Fork the repository

1. Navigate to the kosli-tryout repository (the repository you're currently viewing)
2. Click the "Fork" button in the top-right corner of the page
3. Select your personal GitHub account as the destination
4. Wait for GitHub to complete the fork process
5. You should now have a copy at `https://github.com/YOUR-USERNAME/kosli-tryout`

> :bulb: Ensure you fork to your personal account, not to an organization, for easier management in this tutorial.

#### Enable GitHub Actions and configure secrets

1. Go to your forked repository on GitHub
2. Click on the "Actions" tab
3. If prompted, click "I understand my workflows, go ahead and enable them"
4. The workflow should start automatically, or you can trigger it manually:
   - Click on "Main workflow" in the left sidebar
   - Click "Run workflow" button on the right
   - Select the branch (usually `main`) and click "Run workflow"

> :bulb: GitHub Actions is enabled by default on forks, but you need to explicitly allow workflows to run on your fork for security reasons.

#### Verify the pipeline execution

1. In the "Actions" tab, click on the most recent workflow run
2. Observe the workflow jobs:
   - **Build**: Compiles the Java application using Gradle
   - **Linting**: Checks code quality (may show warnings)
   - **Docker-image**: Builds and pushes a Docker container image
   - **Security-scan**: Scans the Docker image for vulnerabilities
   - **Component-test**: Runs integration tests
   - **Performance-test**: Runs basic performance checks
   - **Deploy**: Starts and stops the application container
3. Wait for all jobs to complete (green checkmarks)
4. If any jobs fail, review the logs to understand what went wrong

> :bulb: The pipeline might take 5-10 minutes to complete on the first run. GitHub Actions provides free minutes for public repositories.

Common issues:
- **Docker-image job fails with permission error**: Make sure your repository has package write permissions enabled
- **Linting shows warnings**: This is expected and won't fail the build (DISABLE_ERRORS is set to true)

#### Explore the GitHub Actions workflow

1. In your repository, navigate to `.github/workflows/full-pipeline.yaml`
2. Review the workflow structure:
   - Notice how it's triggered on every push
   - Observe the environment variables set at the top
   - See how artifacts are shared between jobs using `upload-artifact` and `download-artifact`
   - Note the dependencies between jobs (e.g., Docker-image needs Build)
3. In later labs, you'll add Kosli integration to this pipeline

#### View the Docker image

1. Go to your GitHub profile page
2. Click on "Packages" tab
3. You should see the `kosli-tryout` package
4. Click on it to view details about the Docker image
5. Note the image tag (usually `latest`) and the SHA digest

> :bulb: The Docker image is automatically published to GitHub Container Registry (ghcr.io) by the pipeline.

</details>

### Verification Checklist

Before moving to the next lab, ensure you have:

- ✅ A Kosli account at app.kosli.com with an organization created
- ✅ A forked copy of the kosli-tryout repository under your GitHub account
- ✅ GitHub Actions successfully completed all jobs in the workflow
- ✅ A Docker image published to your GitHub Container Registry
- ✅ Understanding of the basic pipeline structure

### Clean up

No cleanup is required for this lab. The deployed container is automatically stopped by the pipeline's Deploy job.

## Next Steps

In [Lab 2: Flows and Trails](lab-02-flows-and-trails.md), you'll learn how to install the Kosli CLI, create flows and trails, and start integrating Kosli into your CI/CD pipeline.

## Resources

- [Kosli Documentation](https://docs.kosli.com/)
- [Getting Started with Kosli](https://docs.kosli.com/getting_started/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
