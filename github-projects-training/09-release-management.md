# 09 - Release Management (Quản lý Release)

> **Mục tiêu**: Quản lý Release hiệu quả từ GitHub Projects

**Thời lượng**: 45 phút
**Đối tượng**: PM, QA Lead

---

## 🎯 Release trong GitHub Projects

### Release là gì?

**Release** = Phiên bản sản phẩm được deploy lên production, chứa một tập hợp features/bugs đã hoàn thành.

---

## 📋 Pre-Release Checklist (PM)

```markdown
2 tuần trước release:
- [ ] Tất cả issues trong milestone đã ở status "Testing" or "Done"
- [ ] QA regression test complete
- [ ] No critical bugs open
- [ ] Release notes drafted

1 tuần trước:
- [ ] Stakeholder review features on staging
- [ ] PM verify all features
- [ ] Security review (if needed)
- [ ] Performance check

3 ngày trước:
- [ ] Freeze code (no new features)
- [ ] Bug fixes only
- [ ] Final QA smoke test

1 ngày trước:
- [ ] Deployment plan ready
- [ ] Rollback plan ready
- [ ] Team on standby
```

---

## 📝 Release Notes Template

```markdown
# Release v2.5.0 - June 16, 2024

## 🎉 New Features
- [#123] Save for Later in shopping cart
- [#125] Dark mode support
- [#130] Export reports to PDF

## 🔧 Improvements
- [#135] Faster checkout flow (2x speed)
- [#137] Better mobile responsive

## 🐛 Bug Fixes
- [#140] Fix login with Google on Safari
- [#142] Fix checkout crash on iOS
- [#145] Fix typo in email template

## 📊 Metrics
- 12 features shipped
- 8 bugs fixed
- 98% test coverage
- 0 critical bugs

## 🙏 Thanks
Thanks to @dev-alice, @dev-bob, @qa-carol for great work!

## 📦 Installation
```bash
npm install myapp@2.5.0
```

---

**Full changelog**: https://github.com/org/repo/compare/v2.4.0...v2.5.0
```

---

## 🚀 Release Process

### 1. Code Freeze

```
T-3 days: Code freeze
  - No new features
  - Bug fixes only
  - Deploy to staging
```

### 2. QA Final Test

```
T-2 days: QA smoke test
  - Test all features in release
  - Regression test
  - Performance test
  - Sign-off
```

### 3. Deploy Production

```
T-Day: Deploy
  1. Backup database
  2. Deploy code
  3. Run migrations
  4. Smoke test production
  5. Monitor logs (2 hours)
  6. Announce to users
```

### 4. Post-Release

```
T+1 day:
  - Monitor metrics (error rate, performance)
  - Close milestone
  - Retrospective
  - Plan next release
```

---

## 🏷️ GitHub Release Feature

### Tạo Release trên GitHub

```bash
# Using GitHub CLI
gh release create v2.5.0 \
  --title "Release v2.5.0" \
  --notes-file RELEASE_NOTES.md

# Or via GitHub UI
Repository → Releases → Draft new release
  - Tag: v2.5.0
  - Title: Release v2.5.0
  - Description: (paste release notes)
  - Attach binaries (if needed)
  - Publish
```

---

## 📊 Release Metrics

```markdown
Track per release:
- Features shipped
- Bugs fixed
- Test coverage %
- Deployment time
- Rollback count
- Critical bugs in production (target: 0)
```

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu release process
- [ ] Biết viết release notes
- [ ] Biết pre-release checklist
- [ ] Biết cách tạo GitHub release

---

**🚀 Tiếp theo: [10-practical-exercises.md](./10-practical-exercises.md) - Bài tập thực hành**
