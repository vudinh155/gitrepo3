# 04 - PR Review Checklist

> **Mục tiêu**: Checklist review PR để đảm bảo Core principles

**Thời lượng**: 15 phút
**Đối tượng**: Developer, Tech Lead, Reviewer

---

## 📋 Core PR Checklist

### Before Creating PR (Author)

```markdown
- [ ] Branch từ dev (không phải main)
- [ ] Code KHÔNG chứa tên khách hàng
- [ ] Code KHÔNG hard-code business rule riêng
- [ ] Generic & reusable
- [ ] Config-driven / Plugin-based / Hook-based
- [ ] Tests written & pass
- [ ] Self-review code
- [ ] PR description đầy đủ
```

---

### Code Quality (Reviewer)

```markdown
## Architecture
- [ ] KHÔNG vi phạm Core principles
- [ ] Extensible design
- [ ] Follows existing patterns
- [ ] No code duplication

## Code Style
- [ ] Consistent với codebase
- [ ] Readable & maintainable
- [ ] Proper naming
- [ ] Comments where needed

## Testing
- [ ] Unit tests cover new code
- [ ] Integration tests (nếu cần)
- [ ] Edge cases covered
- [ ] Tests pass locally

## Security
- [ ] No security vulnerabilities
- [ ] No sensitive data exposed
- [ ] Input validation
- [ ] SQL injection safe
```

---

### Breaking Changes (Reviewer)

```markdown
- [ ] Có breaking changes không?
- [ ] Nếu CÓ:
  - [ ] Version bump (MAJOR)
  - [ ] Migration guide viết
  - [ ] Clients affected được notify
  - [ ] Backward compatibility considered
```

---

### Documentation (Author & Reviewer)

```markdown
- [ ] README updated (nếu cần)
- [ ] API docs updated
- [ ] Examples provided
- [ ] Config schema documented
- [ ] Changelog updated
```

---

## 📋 Client PR Checklist

### Customization (Reviewer)

```markdown
- [ ] Custom code trong /custom folder
- [ ] KHÔNG modify core code trực tiếp
- [ ] Config overrides rõ ràng
- [ ] No hard-code trong custom code
```

---

### Core Version (Author & Reviewer)

```markdown
- [ ] Core version documented
- [ ] Tested với core version này
- [ ] No breaking changes từ core update
- [ ] Submodule commit hash correct
```

---

### Client-specific (Reviewer)

```markdown
- [ ] Logo/branding đúng client
- [ ] Business rules match requirements
- [ ] Integration tested
- [ ] Config không leak ra Core
```

---

## ✅ Approval Checklist

### Trước khi Approve (Reviewer)

```markdown
- [ ] Đã review toàn bộ code
- [ ] Đã test locally (nếu cần)
- [ ] No concerns về architecture
- [ ] No security issues
- [ ] Questions đã được trả lời
- [ ] CI/CD pass
```

---

### Trước khi Merge (Author)

```markdown
- [ ] Approved bởi >= 1 reviewer
- [ ] All CI checks pass
- [ ] Conflicts resolved
- [ ] Squash commits (nếu cần)
- [ ] Merge message clear
```

---

## 🚨 Red Flags

### Reject ngay nếu thấy

```
🚩 Tên khách hàng trong Core code
🚩 Hard-code business logic cho 1 client
🚩 if (clientId === '...') trong Core
🚩 Copy-paste code không refactor
🚩 Tests không pass
🚩 Breaking changes không document
🚩 Security vulnerabilities
```

---

**🔙 Quay lại: [00-README.md](./00-README.md)**
