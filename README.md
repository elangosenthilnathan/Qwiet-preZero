# Integrating Qwiet AI preZero with GitHub Actions

This guide demonstrates how to integrate [Qwiet AI preZero](https://www.shiftleft.io/) into your GitHub repository to enable automated code analysis using GitHub Actions.

---

## 🚧 Prerequisites

- An existing GitHub repository where you intend to add Qwiet AI preZero for automated code analysis.
- A ShiftLeft ACCESS TOKEN.

---

## 🔑 Step 1: Getting Your Shiftleft Access Token

Every Shiftleft account has this token. Once you [create an account](https://app.shiftleft.io/register), you can find it on the [Organizations](https://app.shiftleft.io/organization/overview) page under the **Overview** tab, as shown in the image below:

 ![SHIFTLEFT ACCESS TOKEN](https://github.com/user-attachments/assets/2f787f0f-957c-4756-91c6-baec87a5af85)

---

## 🔐 Step 2: Create Repository Secrets

GitHub's [secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) are encrypted environment variables that protect sensitive information while making them accessible in GitHub Actions workflows. To set up the necessary secret:

1. Navigate to your GitHub repository.
2. Go to **Settings > Secrets > Actions**.
3. Click **New repository secret**.
4. Create a secret named `SHIFTLEFT_ACCESS_TOKEN` and provide the value of your **SHIFTLEFT_ACCESS_TOKEN**.

>  💡 **Note:** If you plan to add preZero functionality to multiple repositories, consider creating [encrypted secrets for your organization](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-an-organization) to manage them centrally.

---

## ⚙️ Step 3: Set Up GitHub Action Workflow

GitHub [Actions](https://github.com/features/actions) allows you to automate workflows directly within your GitHub repository. To create a new GitHub Action for integrating preZero:

1. Click on the **Actions** tab in your repository.
2. If this is your first time setting up a GitHub Action, click **set up a workflow yourself**; otherwise, click **New workflow**, then select **set up a workflow yourself**.

In the YAML editor, rename the file if desired, and add the following script to invoke preZero. Below is an example for a Python project; adjust the script according to your project's language and build requirements.

```yaml
name: Qwiet AI Static Analysis

on:
  pull_request:
  workflow_dispatch:
  push:

jobs:
  NextGen-Static-Analysis:
    permissions:
      contents: read # Required for actions/checkout to fetch code
      security-events: write # Required for github/codeql-action/upload-sarif to upload SARIF results
      actions: read # Needed in private repositories for github/codeql-action/upload-sarif
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository # This is necessary to access your code
        uses: actions/checkout@v3

      - name: Setup Python # --- Setup Development Environment (Modify as needed) ---
        uses: actions/setup-python@v4
        with:
          python-version: '3.x'


      - name: Qwiet-AllInOne-Scan
      # You may pin to the exact commit or the version.
      # uses: elangosenthilnathan/Qwiet-preZero@29c19b2b8c55db3ec9bf0bc1701a6bd35fa96578
        uses: elangosenthilnathan/Qwiet-preZero@v0.0.1
        with:
          # Qwiet preZero Authentication Token
          shiftleft-access-token: ${{ secrets.SHIFTLEFT_ACCESS_TOKEN }}
          # Regex to exclude path
          excludeRegex: # optional, default is .*(test|Readme).*$
          # Enable SARIF report generation and upload (true/false)
          importGitHub: # optional, default is false
          # Enable Verbose logging
          verbose: # optional, default is false
          # Enable strict mode
          strict: # optional, default is false
          # Enable Synchronous mode
          wait: # optional, default is false
```

---

## ✅ Step 4: Validate Integration
You can trigger the workflow in any of the following ways:

- Create or update a pull request.  
- Push changes to the `main` or `master` branch.  
- Manually trigger the workflow from the **Actions** tab using the **Run workflow** button (if `workflow_dispatch` is enabled).

Once triggered:

- Monitor the workflow execution in the **Actions** tab.  
