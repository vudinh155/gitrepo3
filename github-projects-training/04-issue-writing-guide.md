# 04 - Issue Writing Guide (Hướng dẫn viết Issue chuẩn)

> **Mục tiêu**: Viết Issue mà Dev hiểu ngay, QA test được ngay, không cần hỏi lại

**Thời lượng**: 60 phút
**Đối tượng**: PM, BA, QA Lead

---

## 🎯 Issue tốt là như thế nào?

### 3 tiêu chí vàng

```
1. CLEAR (Rõ ràng)
   → Dev đọc xong biết làm gì

2. TESTABLE (Test được)
   → QA đọc xong biết test gì

3. COMPLETE (Đầy đủ)
   → Không cần hỏi lại PM
```

### Issue tốt trả lời được 5 câu hỏi

```
1. Làm GÌ? (What) → Title + Description
2. Tại SAO? (Why) → Context / User Story
3. Cho AI? (Who) → User persona
4. THẾ NÀO là xong? (Done) → Acceptance Criteria
5. Có GÌ đặc biệt? (Notes) → Technical context, constraints
```

---

## 📝 Template Issue chuẩn

### Template 1: Feature Issue

```markdown
## Title
[Feature] <Tên feature ngắn gọn>

## Description
<Mô tả feature: là gì, tại sao cần>

## User Story
As a <user type>,
I want to <action>,
So that <benefit>.

## Acceptance Criteria
- [ ] <Criterion 1: Điều kiện để feature được coi là done>
- [ ] <Criterion 2>
- [ ] <Criterion 3>

## Design / Mockup
<Link Figma hoặc screenshot>

## Technical Notes (optional)
- API: <endpoint nếu có>
- Database: <thay đổi schema nếu có>
- Dependencies: <library/service cần dùng>

## Test Scenarios (for QA)
- Scenario 1: <Happy path>
- Scenario 2: <Edge case>
- Scenario 3: <Error case>

## Definition of Done
- [ ] Code completed & reviewed
- [ ] Unit tests written
- [ ] QA tested & passed
- [ ] Deployed to production
- [ ] Documentation updated (if needed)

---

**Labels**: feature, <priority>, <team>
**Milestone**: <Sprint X>
**Estimation**: <story points>
```

---

### Template 2: Bug Issue

```markdown
## Title
[Bug] <Tóm tắt bug ngắn gọn>

## Description
<Mô tả bug: hiện tượng gì, ảnh hưởng như thế nào>

## Steps to Reproduce
1. <Bước 1>
2. <Bước 2>
3. <Bước 3>
...

## Expected Behavior
<Hành vi đúng như spec>

## Actual Behavior
<Hành vi thực tế (sai)>

## Environment
- Browser/Device: <Chrome 120 / iPhone 14>
- OS: <macOS 14 / iOS 17>
- URL: <staging.myapp.com/...>
- User account: <test user nếu cần>

## Screenshots / Logs
<Attach screenshot hoặc paste error logs>

## Severity
<Critical / Major / Minor>

## Root Cause (if known)
<Nguyên nhân nếu đã biết>

## Suggested Fix (optional)
<Gợi ý fix nếu có>

---

**Labels**: bug, <severity>, <team>
**Priority**: <High/Medium/Low>
**Milestone**: <Sprint X>
```

---

### Template 3: Task Issue (Technical)

```markdown
## Title
[Task] <Tên task>

## Description
<Mô tả task: làm gì, tại sao cần làm>

## Context
<Background: tại sao cần task này>

## Checklist
- [ ] <Subtask 1>
- [ ] <Subtask 2>
- [ ] <Subtask 3>

## Expected Outcome
<Kết quả mong đợi sau khi task done>

## Technical Details
<Các chi tiết kỹ thuật cần lưu ý>

## Definition of Done
- [ ] Task completed
- [ ] Code reviewed
- [ ] Merged to main
- [ ] Documented (if needed)

---

**Labels**: task, tech-debt, <team>
**Estimation**: <story points>
```

---

## ✅ Ví dụ Issue TỐT

### Ví dụ 1: Feature Issue tốt

```markdown
Issue #234: [Feature] Thêm chức năng "Save for Later" trong giỏ hàng

## Description
Cho phép user lưu sản phẩm để mua sau, không bị mất khi checkout
các sản phẩm khác.

Hiện tại, user phải remove sản phẩm khỏi giỏ nếu chưa muốn mua ngay
→ Mất thời gian tìm lại sau.

## User Story
As a **shopper**,
I want to **save items for later** without removing from my account,
So that **I can easily find and purchase them later**.

## Acceptance Criteria
- [ ] User thấy button "Save for Later" bên cạnh mỗi item trong giỏ
- [ ] Click "Save for Later" → item chuyển sang tab "Saved Items"
- [ ] Tab "Saved Items" hiển thị tất cả items đã save
- [ ] User có thể "Move to Cart" để chuyển item về giỏ
- [ ] Saved items persist sau khi logout/login lại
- [ ] UI match với design trong Figma

## Design / Mockup
Figma: https://figma.com/file/xyz (Frame: "Save for Later")

## Technical Notes
- **API**:
  - POST `/api/cart/save-for-later`
    Body: `{ cart_item_id: 123 }`
  - GET `/api/saved-items`
    Response: `[ { id, product_id, saved_at } ]`

- **Database**:
  Thêm column `saved_for_later_at` (nullable timestamp) vào bảng `cart_items`

- **Frontend**:
  Component mới: `<SavedItemsTab />`

## Test Scenarios (for QA)
1. **Happy path**: User save item → logout → login → vẫn thấy saved item
2. **Edge case**: User save 100 items → pagination hoạt động đúng
3. **Error case**: API down → hiển thị error message "Cannot save item now"

## Definition of Done
- [ ] Backend API implemented & tested
- [ ] Frontend UI implemented (match Figma)
- [ ] QA tested all scenarios & passed
- [ ] Deployed to production
- [ ] API documentation updated

---

**Labels**: feature, high-priority, team:fullstack
**Milestone**: Sprint 20
**Estimation**: 8 points
```

**✅ Tại sao issue này TỐT:**
- Rõ ràng: Dev đọc xong biết làm gì
- Đầy đủ: Có API spec, database schema, design link
- Testable: QA có test scenarios cụ thể
- Context: Giải thích tại sao cần feature
- DoD rõ ràng: Biết khi nào issue done

---

### Ví dụ 2: Bug Issue tốt

```markdown
Issue #567: [Bug] User không thể login bằng Google trên Safari

## Description
User click "Login with Google" trên Safari browser nhưng bị redirect
về homepage thay vì login thành công.

Bug này chỉ xảy ra trên **Safari** (Chrome/Firefox OK).

**Impact**: ~15% users dùng Safari → không thể login → bounce rate cao.

## Steps to Reproduce
1. Mở Safari browser (v17.0)
2. Vào https://staging.myapp.com/login
3. Click button "Login with Google"
4. Popup Google OAuth → chọn account → Click "Allow"
5. Redirected về https://staging.myapp.com/ (homepage)
6. User vẫn chưa login (check: cookie `auth_token` = null)

## Expected Behavior
- User redirected về https://staging.myapp.com/dashboard
- User đã login (cookie `auth_token` tồn tại)
- Header hiển thị tên user

## Actual Behavior
- User redirected về homepage (/)
- User chưa login
- Console log: `redirect_uri_mismatch` error

## Environment
- **Browser**: Safari 17.0 (macOS 14.1)
- **URL**: https://staging.myapp.com/login
- **Tested accounts**: test@example.com, alice@example.com
- **NOT affected**: Chrome 120, Firefox 119

## Screenshots / Logs
Screenshot: (attached)

Console error:
```
Error: redirect_uri_mismatch
  at OAuthHandler.handleCallback (oauth.ts:45)
  Expected: https://staging.myapp.com/auth/google/callback
  Received: https://staging.myapp.com/auth/google/callback/
```

## Severity
**High** - Ảnh hưởng 15% users, không thể login

## Root Cause (Suspected)
Safari append trailing slash `/` vào redirect_uri
→ Không match với redirect_uri đã config trong Google OAuth
→ Google reject → redirect về homepage

## Suggested Fix
Normalize redirect_uri: remove trailing slash trước khi verify
File: `backend/auth/google-oauth.ts` line 42

---

**Labels**: bug, high-priority, team:backend
**Assignee**: @dev-john
**Milestone**: Sprint 20 (Fix ASAP)
```

**✅ Tại sao issue này TỐT:**
- Steps to reproduce rõ ràng → Dev có thể replicate
- Environment cụ thể → Biết bug chỉ xảy ra ở đâu
- Có screenshot + logs → Dev debug nhanh
- Impact rõ ràng → PM prioritize đúng
- Suggested fix → Dev có hướng giải quyết

---

## ❌ Ví dụ Issue TỆ

### Ví dụ 1: Feature Issue tệ

```markdown
Issue #999: Add new feature

Description: We need to add a new feature for users

Labels: feature
```

**❌ Tại sao issue này TỆ:**
- Không rõ feature là gì
- Không có acceptance criteria
- Không có context (tại sao cần)
- Dev không biết làm gì
- QA không biết test gì

**🔧 Cách fix:**
- Viết rõ feature là gì (ví dụ: "Save for Later")
- Thêm User Story
- Thêm Acceptance Criteria
- Thêm design/mockup
- Thêm technical notes nếu cần

---

### Ví dụ 2: Bug Issue tệ

```markdown
Issue #888: Login broken

Description: Login doesn't work

Labels: bug
```

**❌ Tại sao issue này TỆ:**
- Không có steps to reproduce
- Không biết expected vs actual behavior
- Không có environment info
- Dev không thể replicate bug

**🔧 Cách fix:**
- Viết rõ steps to reproduce (1, 2, 3...)
- Expected vs Actual
- Environment (browser, OS, URL)
- Screenshot/logs nếu có

---

## 📐 Best Practices

### 1. Title viết như thế nào?

**✅ Title tốt:**
```
[Feature] Thêm chức năng export PDF cho báo cáo
[Bug] User không thể upload ảnh > 5MB
[Task] Migrate database từ MySQL sang PostgreSQL
[Docs] Viết API documentation cho /api/users
```

**❌ Title tệ:**
```
New feature
Bug
Fix this
Update
```

**Rules:**
- Bắt đầu bằng `[Type]`: Feature, Bug, Task, Docs
- Tóm tắt ngắn gọn (< 10 từ)
- Dùng động từ rõ ràng: Thêm, Sửa, Nâng cấp, Migrate, Viết
- Đủ thông tin để hiểu mà không cần đọc description

---

### 2. Acceptance Criteria viết như thế nào?

**✅ AC tốt:**
```markdown
- [ ] User click "Export PDF" → file PDF download tự động
- [ ] PDF chứa đúng data của báo cáo (table + chart)
- [ ] File name format: "Report_YYYY-MM-DD.pdf"
- [ ] PDF generation < 3 giây cho báo cáo < 1000 rows
- [ ] Hiển thị loading spinner trong khi generate PDF
```

**❌ AC tệ:**
```markdown
- [ ] Feature works
- [ ] User can export
- [ ] PDF is good
```

**Rules:**
- Mỗi AC = 1 điều kiện cụ thể, test được
- Dùng format: "When X → Then Y"
- Có số liệu nếu cần (< 3 giây, > 1000 rows)
- QA đọc xong biết test gì

---

### 3. Description viết như thế nào?

**✅ Description tốt:**
```markdown
## Description
Hiện tại, user phải screenshot báo cáo để share với manager.
Feature này cho phép user export báo cáo dưới dạng PDF,
tiện share qua email hoặc print.

## User Story
As a **sales manager**,
I want to **export sales report to PDF**,
So that **I can share with my team via email**.

## Business Context
- 60% users requested this feature (từ survey Q1 2024)
- Expected to reduce support tickets 20%
- Competitor (Product X) đã có feature này
```

**❌ Description tệ:**
```markdown
Add export feature
```

**Rules:**
- Giải thích **tại sao** cần feature (pain point)
- Viết User Story nếu là feature
- Thêm business context nếu có

---

### 4. Khi nào cần Design/Mockup?

**✅ Cần attach design:**
- Feature có UI mới
- Thay đổi UI hiện tại
- Flow phức tạp (multi-step)

**Không cần design:**
- Bug fix (không đổi UI)
- Backend task (API, database)
- Refactoring

**Format tốt:**
```markdown
## Design
Figma: https://figma.com/file/xyz
Screens:
  - Main screen: Frame "Export PDF - Main"
  - Loading state: Frame "Export PDF - Loading"
  - Error state: Frame "Export PDF - Error"
```

---

### 5. Khi nào cần Technical Notes?

**✅ Cần technical notes khi:**
- PM biết cách implement (suggest cho dev)
- Có constraint đặc biệt (performance, security)
- Cần integration với service bên ngoài
- Database schema thay đổi

**Ví dụ:**
```markdown
## Technical Notes

**API**:
- Endpoint: POST `/api/reports/export-pdf`
- Request body: `{ report_id: 123, format: "pdf" }`
- Response: Binary PDF file

**Performance**:
- Phải handle được báo cáo 10,000 rows
- Timeout: 30 giây
- Nếu > 30s → chuyển sang background job, email PDF sau

**Security**:
- Check user permission trước khi export
- Không expose sensitive data trong PDF
```

---

## 🧪 Practice: Viết Issue

### Bài tập 1: Viết Feature Issue

**Đề bài:**

> Stakeholder yêu cầu: "Chúng ta cần cho phép user đổi password"

**Nhiệm vụ:** Viết feature issue hoàn chỉnh.

**Gợi ý:**
- User Story: As a user, I want to...
- AC: Click "Change Password" → form hiện lên → nhập old + new password → submit → success
- Design: Có form như thế nào? (Figma hoặc sketch)
- Technical: API endpoint? Validation rules?

---

### Bài tập 2: Viết Bug Issue

**Đề bài:**

> QA báo: "Tôi upload ảnh 6MB thì bị lỗi, nhưng 4MB thì OK"

**Nhiệm vụ:** Viết bug issue hoàn chỉnh.

**Gợi ý:**
- Steps to reproduce: 1, 2, 3...
- Expected: Upload thành công (hoặc hiển thị error rõ ràng)
- Actual: Lỗi gì? Error message?
- Environment: Browser, OS, URL?

---

### Bài tập 3: Fix Issue tệ

**Issue tệ:**

```markdown
Issue #123: Login issue

Description: Login not working properly

Labels: bug
```

**Nhiệm vụ:** Viết lại issue này cho đúng chuẩn.

**Checklist:**
- [ ] Title rõ ràng hơn
- [ ] Steps to reproduce
- [ ] Expected vs Actual
- [ ] Environment
- [ ] Severity

---

## 📊 Checklist tự review Issue

Trước khi submit issue, PM tự hỏi:

```markdown
## Clarity (Rõ ràng)
- [ ] Title ngắn gọn, đủ nghĩa
- [ ] Description giải thích rõ làm gì, tại sao
- [ ] Không có thuật ngữ mơ hồ

## Completeness (Đầy đủ)
- [ ] Có Acceptance Criteria
- [ ] Có design/mockup (nếu cần)
- [ ] Có technical notes (nếu cần)
- [ ] Có test scenarios (cho QA)

## Testability (Test được)
- [ ] QA đọc xong biết test gì
- [ ] AC rõ ràng, đo được
- [ ] Có expected behavior

## Metadata
- [ ] Labels đúng (feature/bug/task, priority, team)
- [ ] Milestone/Sprint đã set
- [ ] Estimation (nếu đã planning)

## Linked Issues (nếu có)
- [ ] Link issue liên quan (blocker, related, duplicate)
- [ ] Link epic (nếu là child issue)
```

---

## ❓ FAQ

### **Q1: Issue bao lâu thì quá dài?**

**A**:
- **Lý tưởng**: 200-500 từ
- **Acceptable**: < 1000 từ
- **Quá dài**: > 1500 từ

Nếu quá dài → Có thể chia thành nhiều issue nhỏ hơn.

---

### **Q2: Nên viết tiếng Việt hay tiếng Anh?**

**A**: Tuỳ team.
- **Team Việt Nam**: Tiếng Việt OK (dễ hiểu hơn)
- **Team quốc tế**: Tiếng Anh
- **Quan trọng**: **Nhất quán** trong cả project

---

### **Q3: PM có nên gợi ý technical solution không?**

**A**: **Nên**, nhưng không bắt buộc dev làm theo.
- PM gợi ý → Dev tham khảo
- Dev có thể suggest cách khác tốt hơn
- Technical solution là để dev hiểu rõ hơn, không phải constraint

---

### **Q4: Issue đã tạo rồi, dev bảo thiếu info, PM làm gì?**

**A**:
- Update issue ngay (edit description hoặc comment)
- Mention dev: "@dev-john updated, please check"
- **Đừng tạo issue mới**, vì sẽ mất context

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu 3 tiêu chí issue tốt: Clear, Testable, Complete
- [ ] Biết 3 template: Feature, Bug, Task
- [ ] Biết viết Title, Description, AC chuẩn
- [ ] Làm được ít nhất 1 bài tập practice
- [ ] Có checklist tự review issue

---

**🚀 Tiếp theo:**
- **QA** → Đọc [05-qa-role-and-workflow.md](./05-qa-role-and-workflow.md)
- **PM** → Đọc [08-sprint-management.md](./08-sprint-management.md)
