# Git

## Overview

Git is a distributed version control system for tracking changes in code.

## Basic Concepts

**Repository**: Project with version history

**Commit**: Snapshot of code at a point in time

**Branch**: Independent line of development

**Remote**: Copy of repository on server

**Merge**: Combine branches

## Common Commands

### Setup

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Repository

```bash
# Clone repository
git clone https://github.com/user/repo.git

# Initialize new repository
git init

# Add remote
git remote add origin https://github.com/user/repo.git
```

### Commits

```bash
# Check status
git status

# Stage changes
git add .

# Commit
git commit -m "Descriptive message"

# View history
git log --oneline
```

### Branches

```bash
# Create branch
git checkout -b feature-name

# Switch branch
git checkout main

# List branches
git branch -a

# Delete branch
git branch -d feature-name

# Merge branch
git merge feature-name
```

### Pushing & Pulling

```bash
# Push changes
git push origin branch-name

# Pull changes
git pull origin main

# Fetch updates
git fetch
```

## Branching Strategy

### Main Branch

- Production-ready code
- Tagged releases
- Protected (requires review)
- CI/CD pipeline

### Develop Branch

- Integration branch
- Stable development version
- Feature branches merge here

### Feature Branches

```
feature/new-feature
bugfix/issue-123
hotfix/critical-issue
```

## Commit Best Practices

1. **Atomic commits** - One logical change per commit
2. **Clear messages** - Describe what and why
3. **Sign commits** - GPG signing for security
4. **Frequent commits** - Regular checkpoints
5. **Avoid merge commits** - Use rebase when possible

## Collaborative Workflow

```
1. Create feature branch
   git checkout -b feature/my-feature

2. Make changes and commit
   git add .
   git commit -m "Add feature"

3. Push to remote
   git push origin feature/my-feature

4. Create pull request
   - Code review
   - Run tests
   - Merge when approved

5. Delete branch
   git branch -d feature/my-feature
```

## Advanced Commands

```bash
# Rebase
git rebase main

# Squash commits
git rebase -i HEAD~3

# Cherry-pick
git cherry-pick <commit-hash>

# Stash changes
git stash
git stash pop

# Undo changes
git reset --hard HEAD~1
```

## Best Practices

1. **Meaningful messages** - Searchable commit history
2. **Small PRs** - Easier to review
3. **Code review** - Catch issues early
4. **CI/CD integration** - Automated testing
5. **Documentation** - Keep README updated
6. **Ignore files** - Use .gitignore

