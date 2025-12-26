# 📚 Git Workflow Guide - Giữ Core sạch, Scale dễ dàng

> **Hướng dẫn Git Workflow cho Product/Agency đa khách hàng**

**Dành cho**: MangoAds, MGS, và các sản phẩm có nhiều biến thể khách hàng

---

## 🎯 Mục tiêu

Tài liệu này giúp team:

✅ **Giữ Core sạch** - Không dính vết khách hàng
✅ **Scale dễ dàng** - Thêm client mới không ảnh hưởng Core
✅ **Maintainable** - Code dễ maintain lâu dài
✅ **Collaboration** - Team work hiệu quả, ít conflict
✅ **Quality** - Architecture tốt, ít technical debt

---

## 👥 Dành cho ai?

| Vai trò | Sử dụng để |
|---------|------------|
| **Developer** | Hiểu cách code đúng Core vs Client |
| **Tech Lead** | Review code, đảm bảo nguyên tắc |
| **Architect** | Thiết kế extensible Core |
| **Product Owner** | Hiểu cách tổ chức code base |

---

## 📚 Cấu trúc tài liệu

### 🔴 **Core Principles** (File 01)

| File | Nội dung | Thời lượng |
|------|----------|------------|
| [01-core-client-separation.md](./01-core-client-separation.md) | Nguyên tắc tách Core/Client | 60 phút |

**Nội dung:**
- Triết lý: Core = Product, Client = Customization
- Quy tắc bất biến của Core
- Khi nào PHẢI tách Client repo
- Quy trình update ngược (Client → Core)
- Thiết kế Core extensible
- Git workflow cho Core & Client
- Xử lý conflict đúng cách
- Version management
- Anti-patterns & Best practices

### 🟡 **Supporting Guides** (Files 02-05)

| File | Nội dung | Thời lượng |
|------|----------|------------|
| [02-branch-strategy.md](./02-branch-strategy.md) | Chi tiết Branch Strategy | 30 phút |
| [03-conflict-resolution.md](./03-conflict-resolution.md) | Xử lý conflict từng bước | 30 phút |
| [04-pr-review-checklist.md](./04-pr-review-checklist.md) | Checklist review PR | 15 phút |
| [05-quick-reference.md](./05-quick-reference.md) | Commands & Templates tra cứu nhanh | 15 phút |

---

## 🚀 Lộ trình học

### **Kịch bản 1: Onboarding Developer mới**

```
Ngày 1:
  ✓ Đọc file 01 (Core/Client Separation)
  ✓ Hiểu triết lý: Core sạch, Client riêng
  ✓ Xem examples

Ngày 2-3:
  ✓ Practice: Setup 1 client repo
  ✓ Practice: Add Core as submodule
  ✓ Practice: Custom config

Tuần 2:
  ✓ Code feature đầu tiên
  ✓ Review với Tech Lead
  ✓ Học conflict resolution (file 03)

Tuần 3-4:
  ✓ Code independently
  ✓ Peer review PRs
  ✓ Contribute to Core (nếu phù hợp)
```

---

### **Kịch bản 2: Team Training Session**

```
Session 1 (2h): Core/Client Philosophy
  ✓ Present file 01
  ✓ Discussion: Current pain points
  ✓ Q&A: Scenarios

Session 2 (1.5h): Hands-on Workshop
  ✓ Practice: Tách 1 client repo
  ✓ Practice: Resolve conflicts
  ✓ Review: PR checklist

Session 3 (1h): Advanced Topics
  ✓ Design extensible Core
  ✓ Plugin system
  ✓ Hook mechanism
  ✓ Best practices
```

---

## 🏗️ Architecture Overview

### Model hiện tại (Đang áp dụng)

```
┌─────────────────────────────────────┐
│         Core Product                │
│  (MangoAds / MGS)                   │
│                                     │
│  ✓ Generic features                 │
│  ✓ Extensible architecture          │
│  ✓ Configuration-driven             │
└──────────────┬──────────────────────┘
               │
               │ Submodule/Dependency
               │
      ┌────────┴────────┬──────────────┐
      │                 │              │
  ┌───▼────┐      ┌────▼─────┐   ┌───▼──────┐
  │Client A│      │Client B  │   │Client C  │
  │Repo    │      │Repo      │   │Repo      │
  └────────┘      └──────────┘   └──────────┘
```

---

## ⚡ Quick Start

### 1. Setup Core Repository

```bash
# Clone Core
git clone https://github.com/org/mgs-core.git
cd mgs-core

# Create feature branch
git checkout -b feature/new-auth-policy

# Code...
# Test...
# PR to dev
```

---

### 2. Setup Client Repository

```bash
# Create from template
git clone https://github.com/org/client-template.git mgs-client-abc
cd mgs-client-abc

# Add Core as submodule
git submodule add https://github.com/org/mgs-core.git core
git submodule update --init

# Setup client config
cp custom/config/template.json custom/config/client-abc.json
# Edit config...

# Commit
git add .
git commit -m "Initial setup for Client ABC"
```

---

### 3. Update Core Version in Client

```bash
cd mgs-client-abc/core
git fetch --tags
git checkout v2.6.0
cd ..
git add core
git commit -m "Update core to v2.6.0"
git push
```

---

## 📋 Checklists

### Checklist: Before committing to Core

```markdown
- [ ] Code KHÔNG chứa tên khách hàng
- [ ] Code generic, reusable
- [ ] Config-driven hoặc extensible
- [ ] Tests pass
- [ ] Documentation updated
- [ ] Version bump nếu cần (breaking changes)
```

---

### Checklist: Before creating Client repo

```markdown
- [ ] Đã thử giải quyết bằng config?
- [ ] Đã thử plugin system?
- [ ] Đã thử hook mechanism?
- [ ] Thật sự cần tách repo?
- [ ] Có template client repo?
```

---

### Checklist: Conflict resolution

```markdown
- [ ] Liên hệ người viết code conflict
- [ ] Ngồi cùng nhau (không tự ý resolve)
- [ ] Hiểu ý đồ code của cả hai
- [ ] Thống nhất approach
- [ ] Test cả hai features
- [ ] Document decision trong commit message
```

---

## 🚫 Anti-patterns Phổ biến

### Top 5 Anti-patterns

```
1. ❌ Hard-code tên khách trong Core
   → Phá vỡ generic principle

2. ❌ Quick hack cho deadline
   → Technical debt tích luỹ

3. ❌ Copy-paste từ Client vào Core
   → Code không generic

4. ❌ Branch Client tồn tại mãi mãi trong Core repo
   → Conflict nightmare

5. ❌ Tự ý resolve conflict
   → Break code người khác
```

**→ Xem chi tiết: [01-core-client-separation.md](./01-core-client-separation.md#anti-patterns)**

---

## 💡 Best Practices Summary

```
✅ Core = Product → Giữ sạch, generic
✅ Client = Customization → Tách riêng
✅ Design = Extensible → Config, Plugin, Hook
✅ Conflict = Opportunity → Ngồi cùng resolve
✅ Version = Discipline → Track chặt chẽ
✅ Review = Quality gate → Follow checklist
```

---

## 📊 Success Metrics

Sau khi áp dụng đúng principles này:

```
✅ Core stable - Ít breaking changes
✅ Onboard client mới nhanh - < 1 tuần
✅ Conflict giảm - Team collaborate tốt hơn
✅ Code quality tăng - Generic, reusable
✅ Technical debt giảm - Maintainable long-term
✅ Team velocity tăng - Ít refactor, ít bug
```

---

## 🎯 Key Takeaways

### 3 Nguyên tắc cốt lõi

```
1. Core PHẢI sạch
   → Không dính vết khách hàng

2. Tách là phương án cuối
   → Thiết kế extensible trước

3. Conflict = Cơ hội
   → Review architecture, improve communication
```

---

## 📞 Support & Questions

**Có câu hỏi?**
- Ask Tech Lead trước khi commit vào Core
- Tạo Discussion trong repo
- Tag: `@tech-lead` hoặc `@architect`

**Phát hiện vi phạm Core principles?**
- Tạo Issue: "[Core Violation] <description>"
- PR để fix ngay

**Góp ý cải thiện workflow?**
- Tạo PR cho docs này
- Share trong team meeting

---

## 📝 Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2024-12-21 | Initial version - Core/Client separation guide |

---

## 🎓 Additional Resources

- [Git Submodules Documentation](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [Semantic Versioning](https://semver.org/)
- [Plugin Architecture Patterns](https://martinfowler.com/articles/plugin-architecture.html)
- [Open-Core Business Model](https://en.wikipedia.org/wiki/Open-core_model)

---

**🚀 Sẵn sàng bắt đầu? → Mở [01-core-client-separation.md](./01-core-client-separation.md)**

**📌 Tra cứu nhanh? → Mở [05-quick-reference.md](./05-quick-reference.md)**
