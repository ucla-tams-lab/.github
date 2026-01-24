# UCLA TAMS Lab GitHub Organization - Admin Setup Guide

This guide provides step-by-step instructions for creating and configuring the `ucla-tams-lab` GitHub organization.

## Prerequisites
- GitHub organization owner/admin access
- GitHub CLI (`gh`) installed and authenticated (optional but recommended)
- Git installed

## Part 1: Create GitHub Organization

### Option A: Using GitHub Web UI
1. Go to https://github.com/organizations/new
2. Organization name: `ucla-tams-lab`
3. Display name: `UCLA TAMS Lab`
4. Contact email: (your lab contact email)
5. Choose organization plan (Free or Team/Enterprise as needed)
6. Complete organization creation

### Option B: Using GitHub CLI
```bash
# Note: Organization creation via CLI may require additional permissions
# If this doesn't work, use the Web UI method above
gh api --method POST /organizations \
  -f login=ucla-tams-lab \
  -f admin=YOUR_USERNAME \
  -f profile_name="UCLA TAMS Lab"
```

## Part 2: Configure Organization Settings

### 2.1 Basic Settings (via Web UI)
1. Go to https://github.com/organizations/ucla-tams-lab/settings/profile
2. Set:
   - Display name: `UCLA TAMS Lab`
   - Description: `UCLA TAMS Lab - Human-centered autonomous systems research`
   - URL: (your lab website if available)
   - Email: (lab contact email)

### 2.2 Member Privileges (via Web UI)
1. Go to https://github.com/organizations/ucla-tams-lab/settings/member_privileges
2. Set:
   - Base permissions: **None** (or **Read** if preferred)
   - Repository creation: Allow members to create **private** repositories
   - Repository forking: Configure as needed
   - Pages creation: Allow members to create public pages

### 2.3 Security Settings (via Web UI)
1. Go to https://github.com/organizations/ucla-tams-lab/settings/security
2. Enable:
   - [ ] Require two-factor authentication for everyone
   - [ ] Enable Dependabot alerts
   - [ ] Enable Dependabot security updates
   - [ ] Enable secret scanning (available for public repos and GitHub Advanced Security)

## Part 3: Create Teams

### Using GitHub Web UI
1. Go to https://github.com/orgs/ucla-tams-lab/teams
2. Create the following teams:

**Team: pi**
- Name: `pi`
- Description: `Principal Investigators and organization owners`
- Visibility: `Visible`
- Role: Give this team **Admin** permission on org repositories

**Team: maintainers**
- Name: `maintainers`
- Description: `Repository maintainers and core contributors`
- Visibility: `Visible`
- Role: Give this team **Maintain** permission on org repositories

**Team: members**
- Name: `members`
- Description: `Lab members with repository access`
- Visibility: `Visible`
- Role: Give this team **Write** permission on org repositories (or as needed)

### Using GitHub CLI
```bash
# Create teams
gh api --method POST /orgs/ucla-tams-lab/teams \
  -f name=pi \
  -f description="Principal Investigators and organization owners" \
  -f privacy=closed

gh api --method POST /orgs/ucla-tams-lab/teams \
  -f name=maintainers \
  -f description="Repository maintainers and core contributors" \
  -f privacy=closed

gh api --method POST /orgs/ucla-tams-lab/teams \
  -f name=members \
  -f description="Lab members with repository access" \
  -f privacy=closed
```

## Part 4: Create Repositories

### 4.1 Create `.github` Repository

```bash
# Navigate to the .github-repo directory
cd "/Users/chen_tang/code/Lab Github/.github-repo"

# Initialize git repository
git init
git add .
git commit -m "Initial commit: Organization profile and templates

- Add organization profile README
- Add PR template
- Add issue templates (bug, feature request, question)
- Add SECURITY.md and CONTRIBUTING.md

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"

# Create repository on GitHub (public)
gh repo create ucla-tams-lab/.github --public --source=. --remote=origin

# Push to GitHub
git branch -M main
git push -u origin main
```

**Manual steps (if needed):**
1. Go to https://github.com/organizations/ucla-tams-lab/repositories/new
2. Repository name: `.github`
3. Visibility: **Public**
4. Do NOT initialize with README (we have files locally)
5. Create repository
6. Follow instructions to push existing repository

### 4.2 Create `template-project-website` Repository

```bash
# Navigate to template-project-website directory
cd "/Users/chen_tang/code/Lab Github/template-project-website"

# Initialize git repository
git init
git add .
git commit -m "Initial commit: Project website template

- Jekyll + GitHub Pages template
- Minimal configuration with no plugins
- Placeholder for project page content

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"

# Create repository on GitHub (public, template)
gh repo create ucla-tams-lab/template-project-website --public --source=. --remote=origin

# Push to GitHub
git branch -M main
git push -u origin main
```

**Mark as template repository:**
```bash
# Enable template repository feature
gh api --method PATCH /repos/ucla-tams-lab/template-project-website \
  -f is_template=true
```

**Manual steps (if needed):**
1. Create repository: https://github.com/organizations/ucla-tams-lab/repositories/new
2. Repository name: `template-project-website`
3. Visibility: **Public**
4. Create and push code
5. Go to repository Settings → Check "Template repository"

### 4.3 Create `template-research-code` Repository

```bash
# Navigate to template-research-code directory
cd "/Users/chen_tang/code/Lab Github/template-research-code"

# Initialize git repository
git init
git add .
git commit -m "Initial commit: Research code template

- Language-agnostic structure
- Optional Python scaffold
- CI/CD workflows for sanity checks and Python testing
- Release-to-public checklist

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"

# Create repository on GitHub (public, template)
gh repo create ucla-tams-lab/template-research-code --public --source=. --remote=origin

# Push to GitHub
git branch -M main
git push -u origin main
```

**Mark as template repository:**
```bash
# Enable template repository feature
gh api --method PATCH /repos/ucla-tams-lab/template-research-code \
  -f is_template=true
```

**Manual steps (if needed):**
1. Create repository: https://github.com/organizations/ucla-tams-lab/repositories/new
2. Repository name: `template-research-code`
3. Visibility: **Public**
4. Create and push code
5. Go to repository Settings → Check "Template repository"

## Part 5: Configure Branch Protection

For both template repositories, set up branch protection:

### Using GitHub Web UI
1. Go to repository Settings → Branches
2. Add branch protection rule for `main`:
   - Branch name pattern: `main`
   - [x] Require pull request reviews before merging
   - [x] Require status checks to pass before merging
   - [x] Require branches to be up to date before merging
   - [x] Include administrators (optional)
   - Do not allow force pushes
   - Do not allow deletions

### Using GitHub CLI
```bash
# For template-project-website
gh api --method PUT /repos/ucla-tams-lab/template-project-website/branches/main/protection \
  -f required_pull_request_reviews[dismiss_stale_reviews]=true \
  -f required_pull_request_reviews[require_code_owner_reviews]=false \
  -f enforce_admins=false \
  -f restrictions=null

# For template-research-code
gh api --method PUT /repos/ucla-tams-lab/template-research-code/branches/main/protection \
  -f required_pull_request_reviews[dismiss_stale_reviews]=true \
  -f required_pull_request_reviews[require_code_owner_reviews]=true \
  -f enforce_admins=false \
  -f restrictions=null
```

## Part 6: Verification Checklist

- [ ] Organization `ucla-tams-lab` exists with display name "UCLA TAMS Lab"
- [ ] Organization profile README renders at https://github.com/ucla-tams-lab
- [ ] `.github` repository exists and is public
- [ ] `template-project-website` repository exists, is public, and marked as template
- [ ] `template-research-code` repository exists, is public, and marked as template
- [ ] Teams created: `pi`, `maintainers`, `members`
- [ ] Organization settings configured:
  - [ ] Base permissions set to None/Read
  - [ ] Private-by-default for new repositories
  - [ ] 2FA recommended/required
  - [ ] Dependabot enabled
  - [ ] Secret scanning enabled (if available)
- [ ] Branch protection enabled on `main` for both templates
- [ ] CI workflows pass on both template repositories

## Part 7: Test Template Usage

### Test `template-project-website`
1. Go to https://github.com/ucla-tams-lab/template-project-website
2. Click "Use this template" → "Create a new repository"
3. Create a test repo (can be deleted after)
4. Enable GitHub Pages: Settings → Pages → Source: main branch
5. Verify site builds successfully

### Test `template-research-code`
1. Go to https://github.com/ucla-tams-lab/template-research-code
2. Click "Use this template" → "Create a new repository"
3. Create a test repo (can be deleted after)
4. Verify CI workflows run and pass
5. Verify `ci-python.yml` only runs when `python/` directory exists

## Next Steps for Lab Members

1. Add lab members to the organization
2. Assign members to appropriate teams (pi, maintainers, members)
3. Create first project repository from `template-research-code`
4. Create project website from `template-project-website`
5. Set up any necessary GitHub Actions secrets for CI/CD
6. Configure any additional integrations (Slack, project boards, etc.)

## Notes

- All template repositories are public to allow easy discovery and reuse
- Individual project repositories should be **private by default** until ready for release
- Use the Release-to-Public Checklist in `template-research-code/README.md` before making repos public
- Consider enabling GitHub Advanced Security features if budget allows

## Support

For questions or issues with this setup:
- Check GitHub's organization documentation: https://docs.github.com/en/organizations
- Contact organization admins
- Open an issue in the `.github` repository
