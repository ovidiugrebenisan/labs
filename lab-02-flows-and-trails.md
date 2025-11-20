# Lab 2: Flows and Trails

## Learning Goals

- Install and configure the Kosli CLI on your local machine or GitHub Codespace
- Create and configure an API key for Kosli authentication
- Understand the concepts of Flows and Trails in Kosli
- Create your first Flow using the Kosli CLI
- Begin a Trail manually to track a process execution
- Integrate Flow and Trail creation into your CI/CD workflow

## Introduction

Kosli uses **Flows** and **Trails** to organize and track your software delivery processes:

- A **Flow** represents a repeatable business or software process (like your CI/CD pipeline). It defines what you want to track and what compliance requirements must be met.
- A **Trail** represents a single execution instance of that Flow. For example, each git commit or pull request creates a new Trail that tracks all activities for that specific change.

Think of a Flow as a template for your process, and Trails as the individual instances where you record what actually happened. Together, they provide complete visibility into your software supply chain.

## Prerequisites

- Completed [Lab 1: Get Ready](lab-01-get-ready.md)
- A Kosli account with an organization created
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

<details>
<summary>More Details</summary>

#### Install the Kosli CLI

**On Linux/macOS (local machine):**

```bash
# Download and install using the install script
curl -s https://cli.kosli.com/install.sh | sh

# Verify installation
kosli version
```

**On Windows (local machine):**

```powershell
# Download the Windows binary
Invoke-WebRequest -Uri "https://cli.kosli.com/download/kosli_latest_windows_amd64.zip" -OutFile "kosli.zip"

# Extract and add to PATH (adjust path as needed)
Expand-Archive -Path kosli.zip -DestinationPath "$env:USERPROFILE\kosli"
$env:PATH += ";$env:USERPROFILE\kosli"

# Verify installation
kosli version
```

**Using GitHub Codespace:**

```bash
# The Kosli CLI can be installed in a Codespace
curl -s https://cli.kosli.com/install.sh | sh

# Verify installation
kosli version
```

> :bulb: The Kosli CLI is also available as a GitHub Action (`kosli-dev/setup-cli-action@v2`), which is already used in your workflow. For local testing and manual operations, you need the CLI installed locally.

See [Kosli CLI Installation Guide](https://docs.kosli.com/getting_started/install) for more details.

#### Create a Kosli API Key

1. Log in to [https://app.kosli.com](https://app.kosli.com)
2. Click on your profile icon in the top-right corner
3. Select "Settings" or "User Settings"
4. Navigate to the "API Keys" section
5. Click "Create New API Key"
6. Give it a descriptive name (e.g., "CLI Access" or "Local Development")
7. Copy the generated API key immediately (it won't be shown again)
8. Store it securely

**Configure the API key for CLI use:**

```bash
# Set environment variables (Linux/macOS/Codespace)
export KOSLI_API_TOKEN="your-api-key-here"
export KOSLI_ORG="your-organization-name"

# Add to your shell profile for persistence (~/.bashrc or ~/.zshrc)
echo 'export KOSLI_API_TOKEN="your-api-key-here"' >> ~/.bashrc
echo 'export KOSLI_ORG="your-organization-name"' >> ~/.bashrc
```

```powershell
# Set environment variables (Windows PowerShell)
$env:KOSLI_API_TOKEN="your-api-key-here"
$env:KOSLI_ORG="your-organization-name"

# For persistence, set user environment variables
[System.Environment]::SetEnvironmentVariable('KOSLI_API_TOKEN', 'your-api-key-here', 'User')
[System.Environment]::SetEnvironmentVariable('KOSLI_ORG', 'your-organization-name', 'User')
```

**Add API key as a GitHub Secret:**

1. Go to your forked repository on GitHub
2. Navigate to Settings → Secrets and variables → Actions
3. Click "New repository secret"
4. Name: `KOSLI_API_TOKEN`
5. Value: Your Kosli API key
6. Click "Add secret"
7. Repeat for `KOSLI_ORG` with your organization name

> :bulb: Never commit API keys to your repository. Always use environment variables or secrets management.

See [Kosli Authentication](https://docs.kosli.com/getting_started/install#authentication) for more details.

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

> :bulb: The `--use-empty-template` flag creates a Flow without compliance requirements. In Lab 3, you'll learn how to add a template with specific attestation requirements.

Visit [app.kosli.com](https://app.kosli.com) and navigate to Flows to see your newly created Flow.

See [kosli create flow](https://docs.kosli.com/client_reference/kosli_create_flow/) for more details.

#### Begin a Trail manually

A Trail represents one execution of your process. Let's create a Trail using your latest git commit:

```bash
# Navigate to your repository directory
cd /path/to/your/labs

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

See [kosli begin trail](https://docs.kosli.com/client_reference/kosli_begin_trail/) for more details and [Flows documentation](https://docs.kosli.com/getting_started/flows/) and [Trails documentation](https://docs.kosli.com/getting_started/trails/) for conceptual understanding.

#### Create CI scripts for Flow and Trail management

Now let's integrate Flow and Trail creation into your CI/CD pipeline. Create these shell scripts:

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

The workflow already includes steps to call these scripts, but let's verify they're properly configured:

```yaml
- name: Update Flow
  run: ci/update-flow.sh
  env:
    KOSLI_API_TOKEN: ${{ secrets.KOSLI_API_TOKEN }}
    KOSLI_ORG: ${{ secrets.KOSLI_ORG }}

- name: Kosli start trail
  run: ci/start-trail.sh
  env:
    KOSLI_API_TOKEN: ${{ secrets.KOSLI_API_TOKEN }}
    KOSLI_ORG: ${{ secrets.KOSLI_ORG }}
```

> :bulb: The `env` section ensures the secrets you created are available to the scripts. GitHub Actions automatically provides variables like `GITHUB_SHA` which are mapped to `GIT_COMMIT` in the workflow.

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

</details>

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
