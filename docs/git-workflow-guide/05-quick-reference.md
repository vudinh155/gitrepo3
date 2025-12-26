# 05 - Quick Reference

> **Mục tiêu**: Commands & Templates tra cứu nhanh

---

## 🔧 Git Commands

### Core Repository

```bash
# Clone Core
git clone https://github.com/org/mgs-core.git
cd mgs-core

# Create feature branch from dev
git checkout dev
git pull
git checkout -b feature/auth-policy

# After coding
git add .
git commit -m "feat: Add policy-based auth"
git push origin feature/auth-policy

# Create PR: feature/auth-policy → dev
```

---

### Client Repository với Submodule

```bash
# Clone client repo với submodule
git clone --recursive https://github.com/org/mgs-client-abc.git

# Nếu quên --recursive
git submodule update --init --recursive

# Update core submodule to specific version
cd core
git fetch --tags
git checkout v2.6.0
cd ..
git add core
git commit -m "Update core to v2.6.0"
git push

# Pull latest (bao gồm submodule)
git pull --recurse-submodules
```

---

## 📝 Branch Naming Convention

```
feature/auth-policy          (New feature)
fix/schema-validation        (Bug fix)
refactor/core-scope          (Refactor)
docs/api-documentation       (Documentation)
test/integration-workflow    (Tests)

❌ BAD:
  update
  fix-bug
  client-abc-feature
```

---

## 💬 Commit Message Format

```bash
# Format
<type>: <subject>

# Types
feat:     New feature
fix:      Bug fix
refactor: Code refactoring
docs:     Documentation
test:     Tests
chore:    Maintenance

# Examples
git commit -m "feat: Add policy-based authentication"
git commit -m "fix: Schema validation for null values"
git commit -m "refactor: Extract workflow engine to plugin"
git commit -m "docs: Update API docs for v2.6.0"

# With body
git commit -m "feat: Add policy-based authentication

- Implement PolicyEngine class
- Add permission rules config
- Add tests for edge cases

Closes #123"
```

---

## 📋 Templates

### PR Template (Core)

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Feature
- [ ] Bug fix
- [ ] Refactoring
- [ ] Documentation

## Core Principles Check
- [ ] NO client-specific code
- [ ] Generic & reusable
- [ ] Config-driven / Extensible
- [ ] Tests pass

## Testing
- [ ] Unit tests added/updated
- [ ] Manual testing done
- [ ] All tests pass

## Breaking Changes
- [ ] Yes / No
- If yes: Migration guide below

## Screenshots (if UI changes)

## Checklist
- [ ] Self-reviewed
- [ ] Documentation updated
- [ ] Changelog updated
```

---

### Config Template (Client)

```json
{
  "client": {
    "id": "client-abc",
    "name": "ABC Corporation",
    "branding": {
      "logo": "/custom/assets/logo-abc.png",
      "theme": "blue"
    }
  },
  "features": {
    "auth": {
      "providers": ["google", "azure"],
      "mfa": true
    },
    "workflow": {
      "approvalLevels": 3
    }
  },
  "integrations": {
    "sso": {
      "provider": "azure-ad",
      "config": {
        "tenantId": "xxx"
      }
    }
  }
}
```

---

## 🔍 Useful Commands

### Check for client name in Core

```bash
# Search for potential client names in Core
grep -r "client-abc" src/
grep -r "ABC Corporation" src/

# Should return EMPTY in Core repo
```

---

### Version tagging

```bash
# In Core repo
git tag v2.6.0
git push origin v2.6.0

# List tags
git tag -l

# Delete tag (if mistake)
git tag -d v2.6.0
git push origin :refs/tags/v2.6.0
```

---

### Resolve conflicts

```bash
# When conflict happens
git merge dev
# CONFLICT in file.js

# DON'T auto-resolve
# Contact the other developer
# Resolve together

# After resolving
git add file.js
git commit -m "Resolve conflict in file.js with @dev-bob"
```

---

## ⚡ Quick Checks

### Before committing to Core

```bash
# 1. No client names
grep -r "client-" src/

# 2. No hard-coded IDs
grep -r "clientId ===" src/

# 3. Tests pass
npm test

# 4. Lint pass
npm run lint
```

---

**🔙 Quay lại: [00-README.md](./00-README.md)**
