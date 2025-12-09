# WordPress Theme Push to Test Server

## Overview

This GitHub Actions workflow automatically deploys WordPress theme changes to a test server using CloudPanel deployer when code is pushed to the staging branch.

## Workflow File

Save this as `.github/workflows/deploy-test-server.yml` in your repository:

```yaml
name: Deploy-test-server

# dialog cloudpanel deployer !

env:
  THEME_FOLDER: adventure_git
  TARGET_DIR: /home/adventureteams/htdocs/adventureteams.hostweblankan.in
  THEME_PATH: wp-content/themes
  PERMISSIONS: adventureteams

on:
  push:
    branches:
      - staging
  pull_request:
    branches:
      - staging
  workflow_dispatch:

jobs:

  sonarqube-scan:
    name: sonarqube-scan
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
        with:
          fetch-depth: 0

      - uses: sonarsource/sonarqube-scan-action@master
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
          SONAR_HOST_URL: ${{ secrets.SONAR_HOST_URL }}

      - uses: sonarsource/sonarqube-quality-gate-action@master
        timeout-minutes: 5
        env:
          SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}

  deploy-to-test:
    runs-on: ubuntu-latest
    needs: sonarqube-scan
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup SSH private key
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.SSH_PRIVATE_KEY }}" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa

      - name: Add GitHub and server to known hosts
        run: |
          ssh-keyscan -H ${{ secrets.SSH_HOST }} >> ~/.ssh/known_hosts
          ssh-keyscan -H github.com >> ~/.ssh/known_hosts

      - name: Clone or Pull latest code
        env:
          BRANCH: ${{ github.ref_name }}
        run: |
          ssh -o StrictHostKeyChecking=no ${{ secrets.SSH_USER }}@${{ secrets.SSH_HOST }} "
            set -e

            echo 'Target directory: ${{ env.TARGET_DIR }}/${{ env.THEME_PATH }}/${{ env.THEME_FOLDER }}'
            echo 'Branch: $BRANCH'

            git config --global --add safe.directory \"${{ env.TARGET_DIR }}/${{ env.THEME_PATH }}/${{ env.THEME_FOLDER }}\"

            mkdir -p ~/.ssh
            echo '${{ secrets.SSH_PRIVATE_KEY }}' > ~/.ssh/id_rsa
            chmod 600 ~/.ssh/id_rsa
            ssh-keyscan -H github.com >> ~/.ssh/known_hosts

            if [ -d \"${{ env.TARGET_DIR }}/${{ env.THEME_PATH }}/${{ env.THEME_FOLDER }}/.git\" ]; then
              echo 'Repository exists. Pulling latest changes...'
              cd \"${{ env.TARGET_DIR }}/${{ env.THEME_PATH }}/${{ env.THEME_FOLDER }}\"
              git fetch origin
              git checkout \"$BRANCH\" || git checkout -b \"$BRANCH\" origin/\"$BRANCH\"
              git reset --hard origin/\"$BRANCH\"
            else
              echo 'Cloning repository...'
              rm -rf \"${{ env.TARGET_DIR }}/${{ env.THEME_PATH }}/${{ env.THEME_FOLDER }}\"
              mkdir -p \"${{ env.TARGET_DIR }}/${{ env.THEME_PATH }}/${{ env.THEME_FOLDER }}\"
              GIT_SSH_COMMAND=\"ssh -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no\" \
                git clone -b \"$BRANCH\" git@github.com:${{ github.repository }} \
                \"${{ env.TARGET_DIR }}/${{ env.THEME_PATH }}/${{ env.THEME_FOLDER }}\"
            fi

            echo 'Changing ownership...'
            chown -R ${{ env.PERMISSIONS }}:${{ env.PERMISSIONS }} \
              \"${{ env.TARGET_DIR }}/${{ env.THEME_PATH }}/${{ env.THEME_FOLDER }}\"

            echo 'Deployment completed successfully.'
          "
```

## Environment Variables

These variables need to be customized for each repository:

```yaml
THEME_FOLDER: adventure_git              # Name of your theme folder
TARGET_DIR: /home/adventureteams/htdocs/adventureteams.hostweblankan.in
THEME_PATH: wp-content/themes            # WordPress theme path
PERMISSIONS: adventureteams              # Server user permission
```

## Required GitHub Secrets

Add these secrets to your GitHub repository settings (`Settings → Secrets and variables → Actions`):

### 1. SSH_PRIVATE_KEY
Your SSH private key for server authentication
```
- Go to Settings → Secrets and variables → Actions
- Click "New repository secret"
- Name: SSH_PRIVATE_KEY
- Value: Paste your private SSH key (entire key including -----BEGIN and -----END lines)
```

### 2. SSH_HOST
Your server hostname or IP address
```
Name: SSH_HOST
Value: adventureteams.hostweblankan.in (or your server IP)
```

### 3. SSH_USER
Your SSH username
```
Name: SSH_USER
Value: adventureteams (or your server username)
```

### 4. SONAR_TOKEN
SonarQube authentication token (for code quality scanning)
```
Name: SONAR_TOKEN
Value: Your SonarQube token
```

### 5. SONAR_HOST_URL
SonarQube server URL
```
Name: SONAR_HOST_URL
Value: https://sonarqube.yourserver.com (or your SonarQube URL)
```

## Workflow Jobs

### 1. SonarQube Scan
- Performs code quality analysis
- Must pass before deployment proceeds
- Checks for bugs, vulnerabilities, and code smells

### 2. Deploy to Test Server
- Runs only after SonarQube scan passes
- Clones or pulls latest code from GitHub
- Sets proper file permissions
- Completes deployment to test server

## Trigger Events

- **Push to staging branch** - Automatically deploys on push
- **Pull request to staging branch** - Validates before merge
- **Manual trigger** - Can be run manually via workflow_dispatch

## How to Use in New Repository

### Step 1: Create Workflow File

1. Go to your repository
2. Create `.github/workflows/` directory if it doesn't exist
3. Create new file: `deploy-test-server.yml`
4. Copy the entire workflow file above into this file

### Step 2: Update Environment Variables

Replace these values with your own:

```yaml
env:
  THEME_FOLDER: your_theme_name          # Change to your theme folder
  TARGET_DIR: /home/youruser/htdocs/your-domain.com
  THEME_PATH: wp-content/themes
  PERMISSIONS: youruser                  # Your server username
```

### Step 3: Add SSH Keys to Secrets

**Generate SSH Key Pair** (if you don't have one):

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/github_deploy
# Press enter for passphrase (leave empty for automation)
```

**Add Public Key to Server**:

```bash
# On your server
cat ~/.ssh/github_deploy.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

**Add Private Key to GitHub Secrets**:

1. Copy entire private key content:
   ```bash
   cat ~/.ssh/github_deploy
   ```

2. Go to GitHub repo → Settings → Secrets and variables → Actions

3. Click "New repository secret"

4. Name: `SSH_PRIVATE_KEY`

5. Value: Paste the entire private key (including BEGIN and END lines)

### Step 4: Add Other Secrets

In GitHub repo settings, add these secrets:

| Secret Name | Value |
|------------|-------|
| SSH_HOST | your-server.com or IP address |
| SSH_USER | your-server-username |
| SONAR_TOKEN | Your SonarQube token |
| SONAR_HOST_URL | Your SonarQube server URL |

## Workflow Steps Explained

### 1. Checkout
Pulls your repository code

### 2. SonarQube Scan
- Analyzes code quality
- Must pass before deployment
- Detects bugs and vulnerabilities

### 3. Setup SSH
- Creates SSH directory
- Adds private key with proper permissions (600)

### 4. Known Hosts
- Adds GitHub and server to trusted hosts
- Prevents SSH authentication prompts

### 5. Clone or Pull
- Checks if theme exists on server
- If exists: pulls latest changes from branch
- If new: clones repository
- Changes ownership to specified user

## Deployment Process

1. Developer pushes code to `staging` branch
2. GitHub Actions triggers workflow
3. SonarQube analyzes code
4. If quality gate passes:
   - SSH connects to server
   - Clones or pulls latest code
   - Sets permissions
   - Deployment completes
5. If quality gate fails:
   - Deployment stops
   - Developer must fix issues

## Troubleshooting

### SSH Connection Failed
- Verify SSH_HOST, SSH_USER, SSH_PRIVATE_KEY secrets
- Check if server has public key authorized
- Test SSH manually: `ssh user@host`

### Permission Denied
- Ensure PERMISSIONS variable matches server username
- Verify SSH key has proper permissions on server
- Check authorized_keys format

### Theme Not Updating
- Check if branch name is correct
- Verify Git credentials in SSH key
- Review GitHub Actions logs for errors

### SonarQube Failures
- Fix code quality issues locally
- Re-push to staging branch
- Workflow will retry automatically

## Example Configuration for New Theme

For a new theme called `my_awesome_theme`:

```yaml
env:
  THEME_FOLDER: my_awesome_theme
  TARGET_DIR: /home/webuser/htdocs/mysite.com
  THEME_PATH: wp-content/themes
  PERMISSIONS: webuser
```

Then add secrets:
- SSH_PRIVATE_KEY: (your private SSH key)
- SSH_HOST: mysite.com
- SSH_USER: webuser
- SONAR_TOKEN: (your token)
- SONAR_HOST_URL: https://sonarqube.yourserver.com

## Security Best Practices

✅ **Keep SSH key secure** - Never commit to repository  
✅ **Use strong SSH key** - 4096-bit RSA minimum  
✅ **Restrict permissions** - Only authorized users deploy  
✅ **Monitor deployments** - Review workflow runs  
✅ **Rotate keys regularly** - Change SSH keys periodically  
✅ **Use branch protection** - Prevent direct pushes to main  

## Support

For issues or questions, contact your DevOps team or refer to GitHub Actions documentation.
