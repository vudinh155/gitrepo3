# 02 - Task Creation Standard (Chuẩn hóa tạo Task)

> **Mục tiêu**: Tạo task chuẩn để đo lường được đóng góp

**Thời lượng**: 45 phút
**Đối tượng**: Product Owner, Delivery Manager

---

## 🎯 Tại sao cần chuẩn hóa?

### Vấn đề khi task KHÔNG chuẩn

```
❌ Issue #123: "Fix bug"
   → Không biết ai làm
   → Không biết scope bao nhiêu
   → Không biết khi nào xong
   → Không đo lường được

❌ Issue #456: "Improve performance"
   Assigned to: @alice, @bob, @charlie (3 người)
   → Không biết ai đóng góp bao nhiêu
```

---

## ✅ Nguyên tắc vàng: 1 Issue = 1 đơn vị đo

```
1 Issue = 1 người chịu trách nhiệm chính (owner)
1 Issue = 1 scope rõ ràng
1 Issue = Có estimate
1 Issue = Có type (feature/bug/task)
1 Issue = Có module/component

→ Đo được: Ai làm gì, làm bao nhiêu, chất lượng ra sao
```

---

## 📝 Template Issue chuẩn

### Template tối thiểu (cho đo lường)

```markdown
## Title
[Type][Module] Brief description

Ví dụ:
  - [Feature][Checkout] Add PayPal payment option
  - [Bug][Login] Fix Google OAuth on Safari
  - [Task][Backend] Migrate database to PostgreSQL

## Metadata (Required)

**Type**: Feature | Bug | Task | Refactor
**Module**: Frontend | Backend | Mobile | QA | Infra
**Estimate**: 1 | 2 | 3 | 5 | 8 | 13 (story points)
**Owner**: @username (1 người)
**Sprint**: Sprint 15

## Description

<Mô tả ngắn gọn: làm gì, tại sao>

## Acceptance Criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Definition of Done

- [ ] Code completed
- [ ] PR merged
- [ ] QA tested
- [ ] Deployed
```

---

## 🏷️ Classification: Type & Module

### Type (Loại task)

| Type | Khi nào dùng | Metrics ý nghĩa |
|------|--------------|-----------------|
| **Feature** | Tính năng mới | Feature velocity |
| **Bug** | Fix lỗi | Bug rate, Bug resolution time |
| **Task** | Công việc kỹ thuật | Tech debt reduction |
| **Refactor** | Cải thiện code | Code quality improvement |

**Label convention:**
```
type:feature
type:bug
type:task
type:refactor
```

---

### Module (Component)

```
Ví dụ team E-commerce:

module:auth         (Login, OAuth, Session)
module:checkout     (Cart, Payment, Order)
module:product      (Catalog, Search, Filter)
module:user         (Profile, Settings, Preferences)
module:admin        (Dashboard, Reports, Management)
module:infra        (DevOps, CI/CD, Monitoring)
```

**Lợi ích:**
- Biết ai làm module nào nhiều
- Phân tích bottleneck theo module
- Expert identification

---

## 📊 Estimate: Story Points

### Fibonacci Scale

```
1 point  = 0.5 ngày (vài giờ)
2 points = 1 ngày
3 points = 1-2 ngày
5 points = 2-3 ngày
8 points = 3-5 ngày (cân nhắc chia nhỏ)
13 points = > 5 ngày (NÊN chia nhỏ)
```

### ✅ Estimate tốt

```
Issue #123: [Feature][Checkout] Add PayPal
  Estimate: 5 points

  Breakdown:
    - PayPal API integration: 2 points
    - UI payment form: 1 point
    - Testing: 1 point
    - Documentation: 1 point
```

### ❌ Estimate tệ

```
Issue #456: "Improve system"
  Estimate: ??? (không ước lượng được vì quá mơ hồ)
```

---

## 👤 Owner: 1 issue = 1 owner

### ✅ Làm đúng

```
Issue #123: Add PayPal payment
  Owner: @alice
  Collaborators: @bob (backend API), @charlie (review)

→ Alice chịu trách nhiệm chính
→ Bob & Charlie support
→ Metrics: Issue này tính cho Alice
```

---

### ❌ Làm sai

```
Issue #456: Improve performance
  Assigned to: @alice, @bob, @charlie

→ Không biết ai làm gì
→ Không đo lường được đóng góp từng người
→ Diffusion of responsibility (ai cũng nghĩ người khác làm)
```

---

### Cách handle task cần nhiều người

**Option A: Chia nhỏ**

```
Epic #500: Improve performance

Child issues:
  - #501: Optimize database queries (Owner: @alice)
  - #502: Add Redis caching (Owner: @bob)
  - #503: Frontend lazy loading (Owner: @charlie)

→ Mỗi issue 1 owner
→ Đo được đóng góp từng người
```

**Option B: Dùng Subtasks (nếu task nhỏ)**

```
Issue #123: Setup CI/CD pipeline
Owner: @alice

Subtasks:
  - [ ] Setup GitHub Actions (@alice)
  - [ ] Configure Docker (@bob)
  - [ ] Setup staging env (@charlie)

→ Owner chính: @alice
→ Subtasks: Track contributions in comments
```

---

## 📋 Checklist tạo Issue chuẩn

### PO Checklist

```markdown
Trước khi tạo issue:
  - [ ] Title rõ ràng, có [Type][Module]
  - [ ] Type chính xác (feature/bug/task)
  - [ ] Module specified
  - [ ] Estimate (story points)
  - [ ] Owner assigned (1 người)
  - [ ] AC rõ ràng
  - [ ] DoD checklist

Sau khi tạo:
  - [ ] Add to Project board
  - [ ] Set sprint/milestone
  - [ ] Add relevant labels
```

---

## 🎯 Examples: Good vs Bad Issues

### ❌ BAD Issue

```markdown
Issue #999: Fix stuff

Description: Some things are broken

Assigned: @team

Labels: bug
```

**Vấn đề:**
- Title mơ hồ
- Không có type/module
- Không có estimate
- Assigned to team (không rõ owner)
- Không đo lường được

---

### ✅ GOOD Issue

```markdown
Issue #234: [Bug][Login] Google OAuth fails on Safari

**Type**: Bug
**Module**: auth (Login)
**Severity**: High
**Estimate**: 3 points
**Owner**: @dev-john
**Sprint**: Sprint 15

## Description
Users cannot login via Google OAuth on Safari browser.
Affects ~15% users (Safari market share).

## Steps to Reproduce
1. Open Safari
2. Click "Login with Google"
3. Error: redirect_uri_mismatch

## Expected
User logged in successfully

## Acceptance Criteria
- [ ] Google OAuth works on Safari
- [ ] Tested on Safari 16+17
- [ ] No regression on Chrome/Firefox

## Definition of Done
- [ ] Bug fixed
- [ ] PR merged
- [ ] QA verified
- [ ] Deployed to production

---
Labels: type:bug, module:auth, priority:high
```

**Tốt vì:**
- Title rõ ràng, có type/module
- Owner = 1 người
- Estimate = 3 points
- AC/DoD rõ ràng
- → Đo được: John fix bug này, 3 points, High priority

---

## 🚫 Anti-patterns cần tránh

### 1. **Issue quá lớn**

```
❌ Issue #789: Build entire e-commerce system
   Estimate: 200 points (3 tháng)

✅ Chia nhỏ:
   Epic #789: E-commerce system
     - #790: User authentication (8 pts)
     - #791: Product catalog (13 pts)
     - #792: Shopping cart (8 pts)
     - #793: Checkout flow (13 pts)
     - ...
```

---

### 2. **Issue không có owner**

```
❌ Issue #456: Improve performance
   Assigned: @team

✅ Fix:
   Issue #456: Optimize homepage load time
   Owner: @alice
   Collaborators: @bob (backend), @charlie (review)
```

---

### 3. **Issue mơ hồ**

```
❌ Issue #111: Make it better

✅ Fix:
   Issue #111: [Feature][Search] Add autocomplete
   AC: User types → see suggestions < 100ms
```

---

## ✅ Checklist sau khi đọc xong

```markdown
- [ ] Hiểu nguyên tắc: 1 issue = 1 đơn vị đo
- [ ] Biết template issue chuẩn
- [ ] Biết phân loại: Type & Module
- [ ] Biết estimate bằng story points
- [ ] Biết assign 1 owner/issue
- [ ] Biết chia task lớn thành nhỏ
```

---

**🚀 Tiếp theo: [03-task-status-workflow.md](./03-task-status-workflow.md) - Chuẩn hóa trạng thái task**
