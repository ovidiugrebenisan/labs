# Lab 2: Flows and Trails

## Learning Goals

- Install and configure the Kosli CLI on your local machine or GitHub Codespace
- Create and configure an API key for Kosli authentication
- Understand the concepts of Flows and Trails in Kosli
- Create your first Flow using the Kosli CLI
- Begin a Trail manually to track a process execution
- Integrate Flow and Trail creation into your CI/CD workflow

## Introduction

Now is the time to dig into the world of Kosli.

Kosli uses **Flows** and **Trails** to organize and track your software delivery processes:

- A **Flow** represents a repeatable business or software process (like your CI/CD pipeline). It defines what you want to track and what compliance requirements must be met.
- A **Trail** represents a single execution instance of that Flow. For example, each git commit or pull request creates a new Trail that tracks all activities for that specific change.

Think of a Flow as a template for your process, and Trails as the individual instances where you record what actually happened.

## Prerequisites

- Completed [Lab 1: Get Ready](lab-01-get-ready.md)
- A Kosli account
- Your forked labs repository
- Command line access (local terminal or GitHub Codespace)

## Exercise

### Overview

In this lab, you will:

- Install the Kosli CLI tool
- Generate an API key for authentication
- Create a Kosli Flow to represent your CI/CD pipeline
- Begin a Trail manually using a git commit as the identifier
- Create shell scripts to integrate Flow and Trail management into your GitHub Actions workflow
- Verify the Flow and Trail appear in the Kosli web interface

### Step-by-step instructions

#### Install the Kosli CLI

You can download the correct Kosli CLI for your platform, given that you can run shell scripts on it, by invoking this one-line script:

```bash
curl -fL https://raw.githubusercontent.com/kosli-dev/cli/refs/heads/main/install-cli.sh | sh

# Verify installation
kosli version
```

> :bulb: if this installation method fails, we have other, head over to the [install section of the docs](https://docs.kosli.com/getting_started/install/#part-2-install-kosli-cli) to learn more.

#### Create a Kosli API Key

- Log in to [https://app.kosli.com/settings/profile](https://app.kosli.com/settings/profile)
- Navigate to the "API Keys" section
- Click "Add API Key"
- Give it a descriptive name (e.g., "CLI Access" or "Local Development")
- Copy the generated API key immediately (it won't be shown again)
- Paste it in somewhere you can get it again during these labs, you might need it several times.

**Configure the API key for CLI use:**

<details> 
<summary>Bash</summary>

```bash
# Set environment variables
export KOSLI_API_TOKEN="your-api-key-here"
export KOSLI_ORG="your-gh-username"
```

</details>

See [Kosli Authentication](https://docs.kosli.com/getting_started/install/#assigning-flags-via-environment-variables) for more details.

#### Create a Flow manually

First, let's create a Flow manually to understand the concept:

```bash
# Create a Flow with an empty template
kosli create flow labs-pipeline \
  --description "CI/CD pipeline for labs application" \
  --use-empty-template

# Verify the Flow was created
kosli get flow labs-pipeline
```

You should see something like the following returned:

```bash
Name:                labs-pipeline
Description:         CI/CD pipeline for labs application
Visibility:          private
Template:            
                     version: 1
Last Deployment At:  N/A
Tags:                None
```

> :bulb: The `--use-empty-template` flag creates a Flow without compliance requirements. In Lab 3, you'll learn how to add a template with specific attestation requirements.

Visit [app.kosli.com](https://app.kosli.com) and navigate to Flows to see your newly created Flow.

See [kosli create flow](https://docs.kosli.com/client_reference/kosli_create_flow/) for more details.

#### Begin a Trail manually

A Trail represents one execution of your process. Let's create a Trail using your latest git commit:

```bash
# Begin a Trail using the current git commit SHA
kosli begin trail $(git rev-parse HEAD) \
  --flow labs-pipeline \
  --description "Manual trail for testing"

# Verify the Trail was created
kosli get trail $(git rev-parse HEAD) \
  --flow labs-pipeline
```

The Trail name is the git commit SHA, which uniquely identifies this execution. This allows Kosli to connect all activities (builds, tests, deployments) that happen for this specific commit.

> :bulb: You can use any naming scheme for Trails (commit SHA, PR number, Jira ticket, etc.). Git commits are common because they're unique and tied to your source code.

- Go to Kosli to see your new trail under your Flow called "labs-pipeline"

![first trail](img\first-trail.png)

See [kosli begin trail](https://docs.kosli.com/client_reference/kosli_begin_trail/) for more details and [Flows documentation](https://docs.kosli.com/getting_started/flows/) and [Trails documentation](https://docs.kosli.com/getting_started/trails/) for conceptual understanding.

#TODO: Create something in the trial to see in kosli

#### Create CI scripts for Flow and Trail management

Now let's integrate Flow and Trail creation into your CI/CD pipeline. Create these shell scripts:

**Add API key as a GitHub Secret:**

1. Go to your forked repository on GitHub
2. Navigate to Settings → Secrets and variables → Actions
3. Click "New repository secret"
4. Name: `KOSLI_API_TOKEN`
5. Value: Your Kosli API key
6. Click "Add secret"
7. Repeat for `KOSLI_ORG` with your organization name

> :bulb: Never commit API keys to your repository. Always use environment variables or secrets management.

**Create `ci/update-flow.sh`:**

```bash
#!/bin/bash
set -e

echo "Creating/updating Kosli Flow..."

kosli create flow ${APP_NAME}-pipeline \
  --description "CI/CD pipeline for ${APP_NAME} application" \
  --use-empty-template

echo "Flow created/updated successfully"
```

**Create `ci/start-trail.sh`:**

```bash
#!/bin/bash
set -e

echo "Beginning Kosli Trail for commit ${GIT_COMMIT}..."

kosli begin trail ${GIT_COMMIT} \
  --flow ${APP_NAME}-pipeline \
  --description "Build ${BUILD_NUMBER}: ${GIT_BRANCH}"

echo "Trail started successfully"
```

Make the scripts executable:

```bash
chmod +x ci/update-flow.sh
chmod +x ci/start-trail.sh
```

> :bulb: These scripts are already referenced in your `.github/workflows/full-pipeline.yaml` workflow. The environment variables like `APP_NAME`, `GIT_COMMIT`, and `BUILD_NUMBER` are set at the workflow level.

#### Update GitHub Actions workflow

To avoid repeating the Kosli credentials in every step, let's add them as global environment variables at the top of your workflow file `.github/workflows/full-pipeline.yaml`:

```yaml
env:
  KOSLI_API_TOKEN: ${{ secrets.KOSLI_API_TOKEN }}
  KOSLI_ORG: ${{ secrets.KOSLI_ORG }}
  # ... other existing env vars ...
```

Then verify the steps to call the scripts are configured (note that we don't need to specify `env` here):

```yaml
- name: Update Flow
  run: ci/update-flow.sh

- name: Kosli start trail
  run: ci/start-trail.sh
```

> :bulb: Setting `env` at the top level ensures the secrets are available to all steps in the workflow. GitHub Actions automatically provides variables like `GITHUB_SHA` which are mapped to `GIT_COMMIT` in the workflow.

#### Test the integration

1. Commit the new shell scripts to your repository:

```bash
git add ci/update-flow.sh ci/start-trail.sh
git commit -m "Add Kosli Flow and Trail scripts"
git push origin main
```

2. Go to GitHub Actions and watch the workflow run
3. Verify the "Update Flow" and "Kosli start trail" steps complete successfully
4. Check the logs to see the Kosli CLI output
5. Visit [app.kosli.com](https://app.kosli.com) and navigate to your Flow
6. You should see a new Trail corresponding to your commit

> :bulb: Each push to your repository will now create a new Trail, allowing you to track the complete history of your pipeline executions.

### Verification Checklist

Before moving to the next lab, ensure you have:

- ✅ Kosli CLI installed and can run `kosli version`
- ✅ API key created and configured as environment variables
- ✅ API key and organization name added as GitHub Secrets
- ✅ Successfully created a Flow manually using the CLI
- ✅ Successfully created a Trail manually using a git commit SHA
- ✅ Created `ci/update-flow.sh` and `ci/start-trail.sh` scripts
- ✅ Workflow runs successfully with Flow and Trail creation
- ✅ Can see the Flow and Trails in the Kosli web interface

### Clean up

No cleanup is required for this lab. Flows and Trails remain in Kosli for historical tracking and audit purposes.

## Next Steps

In [Lab 3: Build Controls and Attestations](lab-03-build-controls.md), you'll learn how to attest artifacts and attach evidence like test results, SBOMs, and security scans to your Trails.

## Resources

- [Kosli Flows Documentation](https://docs.kosli.com/getting_started/flows/)
- [Kosli Trails Documentation](https://docs.kosli.com/getting_started/trails/)
- [kosli create flow CLI Reference](https://docs.kosli.com/client_reference/kosli_create_flow/)
- [kosli begin trail CLI Reference](https://docs.kosli.com/client_reference/kosli_begin_trail/)
- [Kosli CLI Installation](https://docs.kosli.com/getting_started/install)
