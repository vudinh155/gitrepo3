# 02 - Branch Strategy

> **Mục tiêu**: Chi tiết Branch Strategy cho Core & Client repositories

**Thời lượng**: 30 phút
**Đối tượng**: Developer, Tech Lead

---

## 🌳 Branch Model Overview

### Core Repository - Git Flow Modified

```
main (production-ready releases)
  │
  ├─── dev (integration branch)
  │     │
  │     ├─── feature/auth-policy
  │     ├─── feature/workflow-engine
  │     ├─── fix/schema-validation
  │     └─── refactor/plugin-system
  │
  └─── hotfix/critical-security-patch
```

**Nguyên tắc:**
- `main` = Chỉ chứa releases (tagged versions)
- `dev` = Integration branch, phát triển chính
- `feature/*` = Nhánh tính năng mới
- `fix/*` = Nhánh sửa bug
- `hotfix/*` = Sửa lỗi khẩn cấp trên production

---

### Client Repository - Simplified Flow

```
main (production cho client này)
  │
  ├─── feature/client-custom-dashboard
  ├─── fix/client-integration-issue
  └─── config/update-branding
```

**Nguyên tắc:**
- `main` = Production cho client cụ thể
- Ít branches hơn (vì ít developer làm việc đồng thời)
- Custom code trong `/custom` folder

---

## 📋 Branch Naming Convention

### Core Repository

```bash
# Feature branches
feature/<short-description>
feature/auth-policy
feature/workflow-engine-v2
feature/export-excel

# Bug fix branches
fix/<issue-description>
fix/schema-validation
fix/null-pointer-error
fix/memory-leak

# Refactor branches
refactor/<component>
refactor/plugin-system
refactor/core-scope-handling

# Documentation branches
docs/<topic>
docs/api-documentation
docs/setup-guide

# Test branches
test/<test-type>
test/integration-workflow
test/load-testing
```

**Rules:**
- Lowercase only
- Hyphens for spaces (không dùng underscore)
- Descriptive but concise (< 30 chars nếu được)
- NO client names trong Core branches

---

### Client Repository

```bash
# Custom features
custom/<feature-name>
custom/dashboard-widgets
custom/reporting-templates

# Config updates
config/<what-config>
config/branding-update
config/sso-integration

# Client-specific fixes
fix/<issue>
fix/integration-error
fix/logo-display
```

---

## 🔄 Branch Lifecycle - Core Repository

### 1. Create Feature Branch

```bash
# Start from latest dev
git checkout dev
git pull origin dev

# Create feature branch
git checkout -b feature/auth-policy

# Verify
git branch
# * feature/auth-policy
#   dev
```

---

### 2. Develop on Feature Branch

```bash
# Code...
# Test...

# Commit regularly
git add .
git commit -m "feat: Add policy engine core"

# Push to remote (backup & visibility)
git push origin feature/auth-policy
```

**Best Practices:**
- Commit often (atomic commits)
- Push daily (backup & visibility)
- Rebase với dev thường xuyên (avoid conflicts)

---

### 3. Keep Feature Branch Updated

```bash
# Pull latest dev changes
git checkout dev
git pull origin dev

# Rebase feature branch
git checkout feature/auth-policy
git rebase dev

# Nếu có conflicts
# → Resolve conflicts
# → git add <resolved-files>
# → git rebase --continue

# Force push (vì rebase rewrites history)
git push origin feature/auth-policy --force-with-lease
```

**⚠️ Warning:**
- Chỉ force push nếu chưa ai review PR
- Nếu đã có review → merge dev vào thay vì rebase

```bash
# Alternative: Merge instead of rebase
git checkout feature/auth-policy
git merge dev
git push origin feature/auth-policy
```

---

### 4. Create Pull Request

```bash
# Đảm bảo tests pass
npm test

# Đảm bảo lint pass
npm run lint

# Push final changes
git push origin feature/auth-policy

# Create PR on GitHub
# feature/auth-policy → dev
```

**PR Checklist:**
- [ ] Tests pass
- [ ] Lint pass
- [ ] Self-reviewed
- [ ] Description đầy đủ
- [ ] No client-specific code (Core repo)

---

### 5. Code Review & Merge

```bash
# Sau khi approved
# Merge vào dev (on GitHub)

# Delete local branch (sau khi merged)
git checkout dev
git pull origin dev
git branch -d feature/auth-policy

# Delete remote branch (optional, GitHub có thể auto-delete)
git push origin --delete feature/auth-policy
```

---

## 🔄 Branch Lifecycle - Client Repository

### Simpler Flow (ít conflicts)

```bash
# 1. Create branch
git checkout -b custom/dashboard-widgets

# 2. Code custom feature
# (trong /custom folder)

# 3. Commit & push
git add custom/
git commit -m "Add custom dashboard widgets for client ABC"
git push origin custom/dashboard-widgets

# 4. PR → main (hoặc merge trực tiếp nếu solo dev)
# 5. Delete branch sau khi merged
```

---

## 🏷️ Release Strategy - Core Repository

### Semantic Versioning

```
v<MAJOR>.<MINOR>.<PATCH>

v2.6.0
  │ │ └─ PATCH: Bug fixes (backward compatible)
  │ └─── MINOR: New features (backward compatible)
  └───── MAJOR: Breaking changes
```

**Examples:**

```bash
v2.5.0 → v2.5.1  # Bug fix
v2.5.1 → v2.6.0  # New feature (backward compatible)
v2.6.0 → v3.0.0  # Breaking change
```

---

### Release Process

```bash
# 1. Merge dev → main
git checkout main
git pull origin main
git merge dev

# 2. Create tag
git tag -a v2.6.0 -m "Release v2.6.0: Add policy-based auth"

# 3. Push tag
git push origin main
git push origin v2.6.0

# 4. Create GitHub Release
# → Attach changelog
# → Document breaking changes
```

---

### Changelog Format

```markdown
# v2.6.0 - 2024-12-21

## Features
- Add policy-based authentication engine
- Support for custom workflow hooks
- Export to Excel feature

## Bug Fixes
- Fix schema validation for null values
- Fix memory leak in workflow engine

## Breaking Changes
- `AuthService` constructor now requires `PolicyConfig`
- Migration guide: [link]

## Upgrade Instructions
```bash
# In client repo
cd core
git fetch --tags
git checkout v2.6.0
cd ..
git add core
git commit -m "Update core to v2.6.0"
```
```

---

## 🚨 Hotfix Strategy

### When to Hotfix

```
Production có critical bug
  → Không thể đợi dev cycle
  → Cần fix & release ngay
```

---

### Hotfix Process

```bash
# 1. Branch từ main (production)
git checkout main
git pull origin main
git checkout -b hotfix/critical-security-patch

# 2. Fix bug
# Code...
# Test thoroughly!

# 3. Commit
git add .
git commit -m "hotfix: Patch critical security vulnerability CVE-2024-XXXX"

# 4. Merge vào BOTH main & dev
# Merge to main
git checkout main
git merge hotfix/critical-security-patch
git tag -a v2.6.1 -m "Hotfix: Critical security patch"
git push origin main
git push origin v2.6.1

# Merge to dev
git checkout dev
git merge hotfix/critical-security-patch
git push origin dev

# 5. Delete hotfix branch
git branch -d hotfix/critical-security-patch
git push origin --delete hotfix/critical-security-patch
```

---

## 🔀 Branch Protection Rules

### Recommended Settings (GitHub)

**Branch: `main`**

```yaml
✅ Require pull request before merging
✅ Require approvals: 2
✅ Require status checks to pass:
   - CI tests
   - Lint
   - Build
✅ Require branches to be up to date
✅ Do not allow bypassing (even admins)
✅ Restrict who can push (Tech Lead only)
```

---

**Branch: `dev`**

```yaml
✅ Require pull request before merging
✅ Require approvals: 1
✅ Require status checks to pass:
   - CI tests
   - Lint
✅ Allow admins to bypass (for hotfixes)
```

---

## 📊 Branch Management Best Practices

### Keep Branches Short-Lived

```
✅ GOOD:
  Feature branch: 1-5 days
  Fix branch: 1-2 days

❌ BAD:
  Feature branch: 30+ days (conflicts nightmare)
```

**Tips:**
- Break large features into smaller PRs
- Merge frequently to avoid drift
- Delete merged branches promptly

---

### Sync Regularly

```bash
# Every morning
git checkout dev
git pull origin dev

git checkout feature/your-branch
git rebase dev  # hoặc git merge dev

# Before creating PR
git pull origin dev --rebase
```

---

### Clear Naming

```bash
✅ GOOD:
  feature/auth-policy
  fix/schema-validation
  refactor/plugin-system

❌ BAD:
  update
  fix-bug
  new-branch
  john-dev
```

---

## 🔍 Common Scenarios

### Scenario 1: Feature branch quá lâu, dev đã xa

```bash
# Option 1: Rebase (clean history)
git checkout feature/old-branch
git fetch origin
git rebase origin/dev

# Resolve conflicts gradually
# Test thoroughly
git push origin feature/old-branch --force-with-lease

# Option 2: Recreate branch (nếu quá nhiều conflicts)
git checkout dev
git pull origin dev
git checkout -b feature/old-branch-v2
git cherry-pick <commits from old branch>
# Resolve conflicts từng commit
```

---

### Scenario 2: Accidentally committed to dev

```bash
# Undo last commit (keep changes)
git reset HEAD~1

# Create proper feature branch
git checkout -b feature/proper-name

# Commit
git add .
git commit -m "feat: Add feature properly"
git push origin feature/proper-name
```

---

### Scenario 3: Need to work on 2 features simultaneously

```bash
# Use multiple branches
git checkout -b feature/feature-a
# Code feature A...
git commit -m "feat: Feature A progress"
git push origin feature/feature-a

# Switch to feature B
git checkout dev
git checkout -b feature/feature-b
# Code feature B...
git commit -m "feat: Feature B progress"
git push origin feature/feature-b

# Switch back to A
git checkout feature/feature-a
```

**⚠️ Warning:**
- Avoid context switching too much
- Finish one feature before starting another (nếu được)

---

### Scenario 4: Feature branch conflict với another merged PR

```bash
# Another PR đã merged vào dev
# Your branch now conflicts

# Update your branch
git checkout feature/your-branch
git fetch origin
git rebase origin/dev

# Conflicts appear
# CONFLICT in src/file.js

# Resolve conflict
# Edit src/file.js manually
# Or use VS Code merge tool

# After resolving
git add src/file.js
git rebase --continue

# Push
git push origin feature/your-branch --force-with-lease
```

---

## 📋 Quick Commands Reference

```bash
# Create branch
git checkout -b feature/name

# Switch branch
git checkout branch-name

# List branches
git branch -a

# Delete local branch
git branch -d branch-name

# Delete remote branch
git push origin --delete branch-name

# Rename branch
git branch -m old-name new-name

# Update branch from dev
git checkout dev && git pull
git checkout feature/name
git rebase dev  # or: git merge dev

# View branch history
git log --oneline --graph --decorate --all
```

---

## ✅ Branch Strategy Checklist

### Before creating branch

```markdown
- [ ] Pulled latest dev
- [ ] Branch name follows convention
- [ ] Purpose is clear (feature/fix/refactor)
```

---

### During development

```markdown
- [ ] Commit regularly (atomic commits)
- [ ] Push to backup
- [ ] Sync with dev periodically
- [ ] Tests pass before pushing
```

---

### Before creating PR

```markdown
- [ ] Rebased/merged latest dev
- [ ] All tests pass
- [ ] Lint pass
- [ ] Self-reviewed all changes
- [ ] No debug code (console.log, etc.)
- [ ] No commented code (unless intentional)
```

---

**🔙 Quay lại: [00-README.md](./00-README.md)**
