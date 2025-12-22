# 04 - Issue-Branch-PR Connection

> **Mục tiêu**: Kết nối Issue → Branch → PR để tracking đầy đủ

**Thời lượng**: 30 phút
**Đối tượng**: PO, DM, Dev

---

## 🎯 Tại sao cần kết nối?

### Tracking đầy đủ

```
Issue #123 → Branch feature/123-add-payment → PR #124 → Merged → Done

→ Trace được:
  - Ai code (PR author)
  - Ai review (PR reviewers)
  - Bao nhiêu commits
  - Bao nhiêu changes
  - Bao lâu (cycle time)
```

---

## 🔗 Quy tắc kết nối

### 1. Branch naming

```
Format:
  feature/issue-number-short-description
  bugfix/issue-number-short-description

Ví dụ:
  feature/123-add-paypal-payment
  bugfix/456-fix-safari-login
```

**Lợi ích:**
- Nhìn branch name biết issue nào
- Auto-link trong GitHub

---

### 2. PR title & body

```markdown
PR Title:
  [Issue #123] Add PayPal payment option

PR Body:
  Closes #123

  ## Changes
  - Added PayPal API integration
  - Added payment UI
  - Added tests

  ## Testing
  - Tested on staging
  - All tests pass
```

**Magic keywords:**
- `Closes #123` → Auto-close issue when PR merged
- `Fixes #456` → Same
- `Resolves #789` → Same

---

### 3. Commit messages

```bash
# Good commit message
git commit -m "feat: Add PayPal integration (ref #123)"
git commit -m "fix: Fix OAuth redirect on Safari (fixes #456)"

# Bad
git commit -m "update"
git commit -m "fix bug"
```

---

## 📊 Metrics từ Connection

### Tracking được

```
Issue #123:
  - Branch: feature/123-add-paypal
  - PR: #124
  - Commits: 8
  - Files changed: 15
  - Lines: +350 / -50
  - Reviews: 2 (Alice, Bob)
  - Review time: 3 hours
  - Cycle time: 2 days (issue start → done)

→ Đầy đủ context
```

---

## ✅ Checklist sau khi đọc

```markdown
- [ ] Hiểu branch naming convention
- [ ] Biết dùng "Closes #123" trong PR
- [ ] Biết commit message format
```

---

**🚀 Tiếp theo: [05-metrics-and-kpis.md](./05-metrics-and-kpis.md)**
