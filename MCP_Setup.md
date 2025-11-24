# 🚀 Complete MCP Setup & Usage Guide

**Model Context Protocol (MCP) Integration with Jenkins & GitHub Copilot**

---

## 📋 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Part 1: Jenkins MCP Plugin Installation](#part-1-jenkins-mcp-plugin-installation)
3. [Part 2: GitHub Personal Access Token](#part-2-github-personal-access-token)
4. [Part 3: Jenkins API Token](#part-3-jenkins-api-token)
5. [Part 4: MCP Configuration File](#part-4-mcp-configuration-file)
6. [Part 5: Environment Variables Setup](#part-5-environment-variables-setup)
7. [Part 6: Verification & Testing](#part-6-verification--testing)
8. [Part 7: Copilot Commands & Usage](#part-7-copilot-commands--usage)
9. [Troubleshooting](#troubleshooting)

---

## Prerequisites

### Required Software:
- ✅ Jenkins (running and accessible)
- ✅ GitHub account with repository access
- ✅ VS Code with GitHub Copilot extension
- ✅ Git installed locally
- ✅ Admin access to Jenkins

### Required Permissions:
- ✅ Jenkins administrator access
- ✅ GitHub repository owner/admin rights
- ✅ Ability to install Jenkins plugins

---

## Part 1: Jenkins MCP Plugin Installation

### Step 1: Access Jenkins Plugin Manager

```
1. Open Jenkins in browser
2. Click "Manage Jenkins" (left sidebar)
3. Click "Plugins"
```

### Step 2: Install MCP Server Plugin

```
1. Click "Available plugins" tab
2. Search: "MCP Server" or "Model Context Protocol"
3. Check the box next to "MCP Server Plugin"
4. Click "Install without restart"
5. Wait for installation to complete (30-60 seconds)
```

### Step 3: Configure MCP Server

```
1. Go to: Manage Jenkins → System (or Configure System)
2. Scroll down to: "MCP Server Settings" section
3. Check: ✅ Enable MCP Server
4. Port: 8080 (default - keep as is)
5. Authentication: ✅ Enable Authentication
6. Click "Save" at the bottom
```

### Verification:

```
Test the endpoint in browser:
https://your-jenkins-server:8080/mcp-server/message

Expected: Authentication error or JSON response (NOT 404)
```

---

## Part 2: GitHub Personal Access Token

### Step 1: Navigate to Token Settings

```
1. Go to: https://github.com
2. Click: Profile picture (top right)
3. Click: Settings
4. Scroll down left sidebar
5. Click: Developer settings (bottom)
6. Click: Personal access tokens
7. Click: Tokens (classic)
8. Click: Generate new token
9. Click: Generate new token (classic)
```

### Step 2: Configure Token

**Token Name:**
```
Copilot MCP Integration
```

**Expiration:**
```
Choose: 90 days (or No expiration for long-term)
```

**Required Scopes (Check these boxes):**

```
✅ repo
   ✅ repo:status
   ✅ repo_deployment
   ✅ public_repo
   ✅ repo:invite
   ✅ security_events

✅ workflow

✅ write:packages
   ✅ read:packages

✅ admin:repo_hook (optional but recommended)
   ✅ write:repo_hook
   ✅ read:repo_hook
```

### Step 3: Generate and Save Token

```
1. Scroll to bottom
2. Click "Generate token"
3. IMMEDIATELY copy the token (shown only once!)
4. Save in password manager or secure location

Token format: ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

⚠️ **IMPORTANT:** Never commit this token to Git or share it publicly!

---

## Part 3: Jenkins API Token

### Step 1: Access User Configuration

```
1. In Jenkins, click your username (top right)
2. Click "Configure"
```

### Step 2: Generate API Token

```
1. Scroll down to "API Token" section
2. Click "Add new Token" button
3. Token Name: "MCP Integration"
4. Click "Generate"
5. IMMEDIATELY copy the token (shown only once!)
6. Save in password manager or secure location

Token format: 11xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Step 3: Save Configuration

```
Click "Save" button at bottom
```

---

## Part 4: MCP Configuration File

### Step 1: Create Configuration File

```
1. Open your project in VS Code
2. In project root directory, create new file: mcp.json
```

### Step 2: Add Configuration

**Copy this content into `mcp.json`:**

```json
{
  "mcp": {
    "servers": {
      "jenkins": {
        "type": "message",
        "url": "https://localhost:8080/mcp-server/message",
        "headers": {
          "Authorization": "${JENKINS_API_TOKEN}"
        }
      },
      "github": {
        "type": "message",
        "url": "https://api.github.com/graphql",
        "headers": {
          "Authorization": "Bearer ${GITHUB_TOKEN}",
          "Content-Type": "application/json"
        },
        "capabilities": {
          "repositories": {
            "read": true,
            "write": true
          },
          "pullRequests": {
            "create": true,
            "update": true,
            "merge": true
          },
          "branches": {
            "create": true,
            "delete": true
          },
          "commits": {
            "create": true
          }
        }
      }
    }
  }
}
```

### Step 3: Update Jenkins URL (if needed)

```
If your Jenkins is NOT on localhost:8080, update the URL:
"url": "https://your-jenkins-server.com:PORT/mcp-server/message"
```

---

## Part 5: Environment Variables Setup

### For Windows (PowerShell)

**Temporary (current session only):**
```powershell
$env:JENKINS_API_TOKEN = "your_jenkins_token_here"
$env:GITHUB_TOKEN = "your_github_token_here"
```

**Permanent (recommended):**
```
1. Press: Win + R
2. Type: sysdm.cpl
3. Press: Enter
4. Click: Advanced tab
5. Click: Environment Variables button
6. Under "User variables", click: New

Variable 1:
  Name: JENKINS_API_TOKEN
  Value: your_jenkins_token_here

Variable 2:
  Name: GITHUB_TOKEN
  Value: your_github_token_here

7. Click OK on all dialogs
8. RESTART VS Code
```

**Via PowerShell Profile (alternative):**
```powershell
# Open profile
notepad $PROFILE

# Add these lines:
$env:JENKINS_API_TOKEN = "your_jenkins_token_here"
$env:GITHUB_TOKEN = "your_github_token_here"

# Save and close
# Restart PowerShell
```

---

### For macOS/Linux (Bash/Zsh)

**Temporary (current session only):**
```bash
export JENKINS_API_TOKEN="your_jenkins_token_here"
export GITHUB_TOKEN="your_github_token_here"
```

**Permanent (recommended):**

**For Bash:**
```bash
# Open bashrc
nano ~/.bashrc

# Add these lines at the end:
export JENKINS_API_TOKEN="your_jenkins_token_here"
export GITHUB_TOKEN="your_github_token_here"

# Save: Ctrl+X, then Y, then Enter
# Reload:
source ~/.bashrc
```

**For Zsh:**
```bash
# Open zshrc
nano ~/.zshrc

# Add these lines at the end:
export JENKINS_API_TOKEN="your_jenkins_token_here"
export GITHUB_TOKEN="your_github_token_here"

# Save: Ctrl+X, then Y, then Enter
# Reload:
source ~/.zshrc
```

---

## Part 6: Verification & Testing

### Step 1: Verify Environment Variables

**Windows (PowerShell):**
```powershell
echo $env:JENKINS_API_TOKEN
echo $env:GITHUB_TOKEN
```

**macOS/Linux:**
```bash
echo $JENKINS_API_TOKEN
echo $GITHUB_TOKEN
```

**Expected:** Both should display your tokens (not empty)

---

### Step 2: Verify MCP Configuration

```bash
# Check file exists
ls mcp.json

# View contents
cat mcp.json
```

**Expected:** File exists and shows your configuration

---

### Step 3: Test Jenkins Connection

**Using curl:**
```bash
curl -H "Authorization: YOUR_JENKINS_TOKEN" \
     https://localhost:8080/api/json
```

**Expected:** JSON response with Jenkins info (NOT 401 error)

---

### Step 4: Test GitHub Connection

**Using curl:**
```bash
curl -H "Authorization: Bearer YOUR_GITHUB_TOKEN" \
     https://api.github.com/user
```

**Expected:** JSON with your GitHub user info

---

### Step 5: Restart VS Code

```
1. Close ALL VS Code windows
2. Wait 5 seconds
3. Reopen VS Code
4. Open your project
```

**This ensures environment variables are loaded!**

---

### Step 6: Test with Copilot

**Open GitHub Copilot Chat in VS Code**

**Type this simple test command:**
```
List all my Jenkins jobs
```

**Expected Response:**
```
Copilot should return a list of your Jenkins jobs
```

**If it works, you're ready! 🎉**

---

## Part 7: Copilot Commands & Usage

### 🔥 **MUST-TRY COMMANDS** (Start Here!)

#### 1. **Build Failure Analysis**
```
Why did my last build fail?
```
```
What caused build #245 to fail?
```
```
Show me the error in the last failed build for payment-service
```

#### 2. **Automatic Fix with PR** ⭐ (MOST POWERFUL)
```
Fix my last build failure, create a branch called fix/build-issue, 
and open a pull request with the corrected code
```
```
Analyze the build failure, generate a fix, create a new branch, 
commit the changes, open a PR, and re-run the build
```
```
The payment-service build failed. Fix it and create a PR.
```

#### 3. **Build Status Checks**
```
What's the status of build #246?
```
```
Show me the status of all builds from today
```
```
Which builds are currently running?
```
```
Show me all failed builds in the last 24 hours
```

#### 4. **Deployments**
```
Trigger a deployment to staging
```
```
Deploy the payment-service to production
```
```
Deploy branch feature/new-api to staging environment
```
```
Start a production deployment for version 2.5.0
```

---

### 💡 **ADVANCED COMMANDS**

#### 5. **Multi-Step Workflows**
```
If all microservices are green, create a release branch 
called release/v2.5 and deploy to staging
```
```
Check if build #245 passed, and if yes, deploy to production
```
```
Create a hotfix branch, cherry-pick commit abc123, and deploy to staging
```

#### 6. **Cross-Service Operations**
```
Show me the status of all microservices in the last build
```
```
Which services have failing tests?
```
```
List all services that were deployed today
```

#### 7. **Test Analysis**
```
Why is test_payment_processing failing?
```
```
Show me all failed tests in build #245
```
```
Which tests have been failing consistently this week?
```
```
Is test_checkout_flow a flaky test?
```

#### 8. **Build Management**
```
Trigger builds for all microservices that changed today
```
```
Restart the last failed build
```
```
Cancel all running builds
```
```
Queue a build for the staging branch
```

#### 9. **Branch & PR Operations**
```
Create a new branch called feature/new-payment from main
```
```
List all open pull requests for payment-service
```
```
What's the status of PR #1234?
```
```
Merge PR #1234 if all checks pass
```

#### 10. **Code & Dependency Analysis**
```
What dependencies does the payment-service use?
```
```
Show me recent commits that might have broken the build
```
```
What changed between build #240 and #250?
```

#### 11. **Performance & Metrics**
```
Compare build times for the last 10 builds
```
```
Show me build duration trends for this week
```
```
Which build took the longest today?
```
```
How long did build #245 take?
```

#### 12. **Emergency Operations**
```
Production is down. Roll back to the last stable version.
```
```
Emergency: Deploy the hotfix branch to production immediately
```
```
Rollback the payment-service to version 2.4.2
```

#### 13. **Release Management**
```
Create release notes for version 2.5.0
```
```
List all changes between build #240 and #250
```
```
Generate a changelog for this sprint
```
```
What features were deployed in the last release?
```

#### 14. **Compliance & Auditing**
```
Show me all production deployments from last month
```
```
Who deployed version 2.4.5 to production?
```
```
Generate an audit report for Q4 deployments
```
```
List all builds triggered by user john.doe
```

#### 15. **Batch Operations**
```
Trigger builds for all microservices
```
```
Deploy all services to staging
```
```
Analyze failures for payment-service, user-service, and auth-service
```

---

### 🎯 **COMPLEX REAL-WORLD SCENARIOS**

#### Scenario 1: Complete Build Fix Workflow
```
The last build for payment-service failed. Analyze the logs, 
identify the issue, create a fix in a new branch called 
fix/payment-bug, open a PR with a detailed description, 
trigger a new build, and notify me when it passes.
```

#### Scenario 2: Release Preparation
```
Check if all microservices have passing builds. If yes, create 
a release branch called release/v3.0, update version numbers, 
create release notes from commits, and trigger staging deployment.
```

#### Scenario 3: Hotfix Deployment
```
There's a critical bug in production. Create a hotfix branch from 
the production tag, apply the fix from PR #456, run tests, and 
if they pass, deploy to production.
```

#### Scenario 4: Multi-Service Investigation
```
Several builds failed in the last hour. Identify which services 
are affected, categorize the failures by type (compilation, tests, 
dependency), and create a summary report.
```

#### Scenario 5: Automated Testing Strategy
```
Analyze all test failures from this week and identify which tests 
are flaky vs real failures. Create tickets for flaky tests that 
need fixing.
```

---

### 📊 **INFORMATION & REPORTING**

#### Build Information
```
Show me details about build #245
```
```
What parameters were used for the last build?
```
```
Show me the console output for build #245
```
```
List all artifacts from build #245
```

#### Repository Information
```
List all branches in the payment-service repository
```
```
Show me recent commits on the main branch
```
```
What files changed in the last commit?
```

#### Pipeline Information
```
Show me the pipeline configuration for payment-service
```
```
List all stages in the deployment pipeline
```
```
What jobs are part of the CI/CD pipeline?
```

---

### 🔧 **DEBUGGING COMMANDS**

```
Show me the full error trace from build #245
```
```
What line in the code caused the build to fail?
```
```
Show me the dependency tree for payment-service
```
```
What environment variables are set for this build?
```
```
Compare the configuration between passing build #244 and failing build #245
```

---

### 🚀 **OPTIMIZATION COMMANDS**

```
Which builds are consuming the most resources?
```
```
How can we speed up the payment-service build?
```
```
Identify bottlenecks in the CI/CD pipeline
```
```
Show me build queue wait times
```

---

## 💡 **TIPS FOR EFFECTIVE COMMANDS**

### ✅ **DO:**
- Be specific about build numbers, service names, or branches
- Use natural language - talk to Copilot like a colleague
- Combine multiple actions in one command for complex workflows
- Ask for explanations when you don't understand something

### ❌ **DON'T:**
- Use vague commands like "fix everything"
- Expect Copilot to guess which service/build you mean
- Chain too many unrelated operations (break into steps)
- Forget to verify critical actions (like production deploys)

---

### 🎯 **Command Templates**

Use these templates and fill in your specifics:

```
Analyze [BUILD_NUMBER] and explain why it failed
```
```
Fix [SERVICE_NAME] and create a PR called [BRANCH_NAME]
```
```
Deploy [SERVICE_NAME] to [ENVIRONMENT]
```
```
Show me [METRIC] for [BUILD_NUMBER]
```
```
Compare [BUILD_A] and [BUILD_B]
```
```
If [CONDITION], then [ACTION]
```

---

## Troubleshooting

### Issue: "Copilot doesn't have access to Jenkins"

**Check:**
```bash
# 1. Verify mcp.json exists
ls mcp.json

# 2. Verify environment variables
echo $JENKINS_API_TOKEN
echo $GITHUB_TOKEN

# 3. Restart VS Code completely
```

---

### Issue: "Connection refused" to Jenkins

**Check:**
```bash
# 1. Is Jenkins running?
curl https://localhost:8080

# 2. Is MCP endpoint accessible?
curl https://localhost:8080/mcp-server/message

# 3. Verify Jenkins MCP plugin installed
# Jenkins → Manage Jenkins → Plugins → Installed → Search "MCP"
```

---

### Issue: "GitHub authentication failed"

**Check:**
```bash
# Test token manually
curl -H "Authorization: Bearer YOUR_TOKEN" https://api.github.com/user

# If fails, regenerate token with correct scopes:
# repo, workflow, write:packages
```

---

### Issue: "Copilot response is very slow"

**Causes:**
- Large build logs (>100MB)
- Slow network connection
- Jenkins server overloaded

**Solution:**
- Try simpler command first
- Check network: `ping jenkins-server.com`
- Reduce log retention in Jenkins settings

---

### Issue: "PR creation fails"

**Check:**
```bash
# 1. Does branch already exist?
git branch -a | grep "branch-name"

# 2. Delete if exists
git branch -D branch-name
git push origin --delete branch-name

# 3. Try again with different branch name
```

---

### Issue: "Environment variables not loaded"

**Solution:**
```bash
# CRITICAL: Must restart VS Code after setting env vars!

# Windows: Close all windows, reopen
# Mac/Linux: Quit completely (Cmd+Q), reopen

# Verify after restart:
echo $JENKINS_API_TOKEN
echo $GITHUB_TOKEN
```

---

## 📚 Additional Resources

### Documentation:
- MCP Official Docs: https://modelcontextprotocol.io/docs
- Jenkins MCP Plugin: https://github.com/jenkinsci/mcp-server-plugin
- GitHub REST API: https://docs.github.com/en/rest

### Support:
- MCP Discord: https://discord.gg/modelcontextprotocol
- Jenkins Community: https://community.jenkins.io
- GitHub Support: https://support.github.com

---

## 🎉 Success Checklist

**You're fully set up when you can:**

- [ ] Ask Copilot: "List all Jenkins jobs" → Get response
- [ ] Ask Copilot: "Why did my last build fail?" → Get analysis
- [ ] Ask Copilot: "Fix the build and create a PR" → PR is created
- [ ] Ask Copilot: "Trigger a deployment" → Build starts
- [ ] Ask Copilot: "Show build status" → Get current status

**If all 5 work, you're done! 🚀**

---

## 🎯 Next Steps

1. Start with simple commands to get comfortable
2. Gradually try more complex workflows
3. Create custom aliases for your frequent operations
4. Train your team on the most useful commands
5. Track time savings and ROI

---

**Happy automating! 🚀**

*Last updated: November 2025*
