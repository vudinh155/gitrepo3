# 05 - QA Role & Workflow

> **Mục tiêu**: Hiểu vai trò QA trong GitHub Projects và quy trình testing chuẩn

**Thời lượng**: 60 phút
**Đối tượng**: QA Engineer, QA Lead, Tester

---

## 🎯 Vai trò của QA trong GitHub Projects

### QA không chỉ là "người bấm nút test"

QA trong GitHub Projects là:

- 🛡️ **Quality Gatekeeper**: Bảo vệ chất lượng sản phẩm trước khi release
- 🔍 **Bug Hunter**: Tìm bug trước khi user gặp
- 📝 **Documentation Helper**: Verify rằng feature match với spec
- 🤝 **PM's Partner**: Giúp PM validate requirement
- 📊 **Metrics Tracker**: Theo dõi quality metrics (bug rate, coverage, etc.)

### QA KHÔNG làm gì?

- ❌ Không chỉ test UI (phải test cả logic, edge case, performance)
- ❌ Không close issue mà chưa test regression
- ❌ Không approve PR nếu chưa hiểu code thay đổi gì
- ❌ Không "pass" issue để cho kịp deadline

---

## 🚀 Quy trình QA chuẩn trong GitHub Projects

### Big Picture: Issue → Testing → Release

```
┌──────────────┐
│ Issue Created│ (PM tạo issue với AC rõ ràng)
└──────┬───────┘
       ↓
┌──────────────┐
│ Dev Coding   │ (Dev code & tạo PR)
└──────┬───────┘
       ↓
┌──────────────┐
│ Code Review  │ (Dev review code)
└──────┬───────┘
       ↓
┌──────────────┐
│ PR Merged    │ (Code merge vào main/staging branch)
└──────┬───────┘
       ↓
┌──────────────┐
│ QA TESTING   │ ← QA bắt đầu vào đây
│              │
│ 1. Read AC   │
│ 2. Read PR   │
│ 3. Test      │
│ 4. Report    │
└──────┬───────┘
       ↓
   ┌───────┐
   │ Pass? │
   └───┬───┘
       ↓
   Yes │ No
       │  └──→ Create Bug Issue → Dev Fix → QA Retest
       ↓
┌──────────────┐
│ Done         │ (Issue closed, ready for release)
└──────────────┘
```

---

## 📋 Workflow chi tiết cho QA

### Bước 1: Nhận Issue để test

#### QA nhận issue khi nào?

```
Trigger: Issue chuyển sang status "Testing" (hoặc "Ready for QA")
```

**Cách QA biết có issue mới để test:**

**Option A: Tự động (recommended)**
- Setup GitHub notification: Issue assigned to @qa-team → notify
- Dev mention QA trong comment: "@qa-alice please test #123"

**Option B: Thủ công**
- QA check Project Board view "Testing" column mỗi ngày
- Daily standup: Dev announce "PR #456 merged, QA can test"

---

### Bước 2: Đọc & Hiểu Issue

**QA PHẢI đọc trước khi test:**

#### ✅ Checklist đọc issue

```markdown
- [ ] Đọc **Description**: Feature là gì? Tại sao cần?
- [ ] Đọc **Acceptance Criteria**: Điều kiện nào để pass?
- [ ] Đọc **Design/Mockup**: UI thế nào? Flow ra sao?
- [ ] Đọc **Technical Notes**: Có constraint gì đặc biệt?
- [ ] Đọc **PR linked**: Code thay đổi gì? Scope bao nhiêu?
```

#### ⚠️ Nếu issue không rõ ràng → QA làm gì?

```
1. Comment trong issue:
   "@pm-bob AC này chưa rõ: 'User có thể save item'
    → Save ở đâu? DB? LocalStorage?
    → Có limit số lượng không?"

2. Đợi PM clarify

3. KHÔNG test nếu chưa rõ
   (Test mà không hiểu spec = waste time)
```

---

### Bước 3: Chuẩn bị Test

#### A. **Setup môi trường test**

```markdown
QA Checklist:
  - [ ] Biết test ở đâu: Staging? Local? Dev environment?
  - [ ] Có test account (nếu cần login)
  - [ ] Có test data (nếu cần)
  - [ ] Biết version/branch nào: `git log` or tag release
```

#### B. **Viết Test Plan (cho feature lớn)**

Template test plan ngắn:

```markdown
## Test Plan for Issue #123: Save for Later

### Test Scope
- Save item functionality
- View saved items
- Move saved item back to cart
- Data persistence

### Out of Scope (v1)
- Notification khi giá giảm
- Share saved items

### Test Cases

#### TC-001: Save item to "Saved Items"
- **Precondition**: User logged in, có 3 items trong cart
- **Steps**:
  1. Click "Save for Later" ở item #1
  2. Verify item #1 chuyển sang tab "Saved Items"
  3. Verify cart còn 2 items
- **Expected**: ✅ Item saved, cart update

#### TC-002: Saved items persist sau logout
- **Steps**:
  1. Save item #1
  2. Logout
  3. Login lại
  4. Vào tab "Saved Items"
- **Expected**: ✅ Vẫn thấy item #1

#### TC-003: Move saved item back to cart
- **Steps**:
  1. Có item #1 trong "Saved Items"
  2. Click "Move to Cart"
  3. Verify item #1 về cart
- **Expected**: ✅ Item back to cart

### Edge Cases
- Save 100 items → pagination work?
- Save item → product deleted → hiển thị gì?
- API timeout → error message rõ ràng?
```

---

### Bước 4: Thực hiện Test

#### A. **Test theo AC (Acceptance Criteria)**

**Rule #1**: **Mỗi AC phải test ít nhất 1 lần**

Ví dụ issue có AC:

```markdown
- [ ] User click "Save for Later" → item chuyển sang tab "Saved Items"
- [ ] Tab "Saved Items" hiển thị tất cả items đã save
- [ ] Saved items persist sau logout/login
```

→ QA test 3 cases tương ứng

#### B. **Test các trường hợp đặc biệt**

Ngoài AC, QA cần test:

**1. Happy Path**
```
User flow chuẩn: Login → Add to cart → Save for later → Logout → Login → Check
```

**2. Edge Cases**
```
- Empty state: Chưa có saved item nào → hiển thị gì?
- Max limit: Save 1000 items → performance?
- Concurrent: 2 tab cùng save item → sync đúng không?
```

**3. Error Cases**
```
- API down → hiển thị error message?
- Network slow → loading state?
- Invalid data → handle gracefully?
```

**4. Cross-browser / Cross-device**
```
- Chrome, Firefox, Safari
- Desktop, Mobile, Tablet
```

**5. Regression**
```
- Feature mới có làm hỏng feature cũ không?
- Login vẫn work?
- Checkout vẫn work?
```

---

### Bước 5: Ghi nhận kết quả

#### A. **Nếu PASS ✅**

Comment trong issue:

```markdown
**QA Test Result: ✅ PASS**

Tested on: 2024-06-15 14:30
Environment: https://staging.myapp.com
Tested by: @qa-alice

**Test Summary:**
- ✅ TC-001: Save item → PASS
- ✅ TC-002: Persist after logout → PASS
- ✅ TC-003: Move back to cart → PASS
- ✅ Regression: Login, Checkout → PASS

**Browsers tested:**
- ✅ Chrome 120 (macOS)
- ✅ Safari 17 (macOS)
- ✅ Firefox 119 (Windows)

**Devices tested:**
- ✅ Desktop (1920x1080)
- ✅ Mobile (iPhone 14, Safari)

**Notes:**
- Minor UI issue: Button text "Save for Later" bị truncate on mobile (< 375px width)
  → Created issue #124 for tracking
```

**Actions:**
- Update issue status: Testing → Done
- Mention PM: "@pm-bob Verified, ready for release"

---

#### B. **Nếu FAIL ❌**

**QA KHÔNG close issue, mà:**

1. Comment trong issue:

```markdown
**QA Test Result: ❌ FAIL**

Tested on: 2024-06-15 14:30
Environment: https://staging.myapp.com
Tested by: @qa-alice

**Failed Test Cases:**
❌ TC-002: Saved items KHÔNG persist sau logout

**Steps to Reproduce:**
1. Login as test@example.com
2. Save item #1 (product ID: 456)
3. Logout
4. Login lại
5. Check tab "Saved Items" → **Empty** (expected: có item #1)

**Expected:** Item #1 vẫn hiển thị
**Actual:** Tab "Saved Items" empty

**Severity:** High (core functionality broken)

---

Created bug issue: #125
Assigning back to @dev-john for fix.
```

2. Tạo Bug Issue riêng (xem file 06)

3. Update status: Testing → In Progress (hoặc Blocked)

4. Mention Dev: "@dev-john Please check bug #125"

---

### Bước 6: Retest sau khi Dev fix

**Flow:**

```
Bug #125 reported → Dev fix → PR merged → QA Retest
```

**QA retest checklist:**

```markdown
- [ ] Retest failed test case → PASS?
- [ ] Test regression: Feature khác còn work?
- [ ] Verify fix không introduce bug mới
```

**Comment trong bug issue:**

```markdown
**Retest Result: ✅ PASS**

Retested on: 2024-06-16 10:00
Environment: staging

✅ TC-002 now PASS: Items persist after logout
✅ Regression PASS

Closing bug. Original issue #123 ready for release.
```

---

## 🐛 QA tìm thấy Bug → Làm gì?

### Option A: Bug liên quan trực tiếp đến issue đang test

**Tạo Bug Issue mới + Link với issue gốc**

```markdown
Issue #125: [Bug] Saved items không persist sau logout

**Related to:** #123 (Save for Later feature)

Description: (xem file 06-bug-reporting-guide.md)
```

### Option B: Bug KHÔNG liên quan (phát hiện khi regression test)

**Tạo Bug Issue độc lập**

```markdown
Issue #126: [Bug] Checkout button missing trên Safari

Description: (không liên quan #123, nhưng phát hiện khi test regression)
```

**⚠️ Lưu ý:**
- Bug mới → Tạo issue mới
- KHÔNG comment bug không liên quan vào issue đang test
- Link issues nếu có related: "Related to #123"

---

## 📊 QA View trong GitHub Projects

### QA nên tạo View riêng để tracking

#### View 1: QA Testing Queue

```
Filter:
  Status = "Testing" OR Status = "Ready for QA"

Sort:
  Priority DESC, Created Date ASC

Columns:
  - Title
  - Priority
  - Assignee (Dev)
  - PR Link
  - AC Count
```

→ Đây là danh sách issues QA cần test

---

#### View 2: QA Blocked Issues

```
Filter:
  Status = "Blocked" AND Labels contains "qa"

Columns:
  - Title
  - Blocked Reason (custom field)
  - Blocked Since (date)
```

→ Issues bị block chờ PM/Dev clarify

---

#### View 3: Bugs Found by QA

```
Filter:
  Labels contains "bug" AND Reporter = @qa-team

Group by:
  Severity (Critical, High, Medium, Low)

Columns:
  - Title
  - Severity
  - Status
  - Assignee
```

→ Track tất cả bugs QA tìm thấy

---

## 🎯 QA Best Practices

### ✅ Làm đúng

#### 1. **Đọc PR trước khi test**

```
QA không chỉ test UI, mà phải hiểu:
  - Code thay đổi gì? (đọc PR diff)
  - File nào bị sửa?
  - Logic mới như thế nào?

→ Test sâu hơn, tìm được bug dev chưa thấy
```

#### 2. **Test regression LUÔN**

```
Mỗi lần test feature mới:
  - Test feature mới PASS
  - Test ít nhất 3-5 features cũ (smoke test)
  - Đảm bảo code mới không break code cũ
```

#### 3. **Ghi nhận rõ ràng**

```
Comment result trong issue:
  ✅ PASS or ❌ FAIL
  + Test cases
  + Environment
  + Screenshots (nếu có bug)
```

#### 4. **Communicate proactively**

```
QA thấy issue không rõ → Hỏi ngay, đừng đợi
QA tìm thấy bug critical → Mention PM + Dev ngay
QA block → Update status "Blocked" + comment lý do
```

#### 5. **Track metrics**

```
QA Lead nên track:
  - Bug found per sprint
  - Bug escape rate (bugs in production)
  - Test coverage %
  - Average test time per issue
```

---

### ❌ Làm sai (Anti-patterns)

#### 1. **Chỉ test happy path**

```
❌ Sai:
  QA test: Login → Success → PASS

✅ Đúng:
  QA test:
    - Happy: Login success
    - Error: Wrong password → error message
    - Edge: Empty email → validation
    - Edge: SQL injection → blocked
```

#### 2. **PASS issue mà chưa đọc PR**

```
❌ Sai:
  QA: "UI đẹp, PASS"
  (Nhưng không biết code thay đổi cả logic backend)

✅ Đúng:
  QA đọc PR → thấy logic thay đổi
  → Test cả API, database, không chỉ UI
```

#### 3. **Không test regression**

```
❌ Sai:
  QA test feature mới PASS → Close issue
  (Không biết feature mới làm hỏng login)

✅ Đúng:
  QA test feature mới + regression
  → Phát hiện login bị break
  → Report bug → Dev fix
```

#### 4. **Bug report thiếu info**

```
❌ Sai:
  "Bug: Login không work"

✅ Đúng:
  "Bug: User không thể login bằng Google trên Safari
   Steps: 1, 2, 3...
   Expected: ...
   Actual: ..."
```

(Xem file 06-bug-reporting-guide.md)

---

## 📈 Metrics QA nên theo dõi

### Sprint-level Metrics

| Metric | Cách đo | Target |
|--------|---------|--------|
| **Bugs Found** | Số bug QA tìm trong sprint | Track trend |
| **QA Pass Rate** | % issues pass lần đầu | > 80% |
| **Retest Rate** | % issues phải test lại (do bug) | < 20% |
| **Avg Test Time** | Thời gian test 1 issue | < 2 giờ |

### Quality Metrics

| Metric | Cách đo | Target |
|--------|---------|--------|
| **Bug Escape Rate** | % bugs found in production / total bugs | < 10% |
| **Critical Bugs** | Số bugs critical trong sprint | 0 |
| **Test Coverage** | % features có test case | > 90% |

---

## 🧪 QA trong Sprint Ceremonies

### Daily Standup

**QA update:**
```
Yesterday:
  - Tested issue #123 → PASS
  - Found bug #125 (login broken)

Today:
  - Will retest #125 after dev fix
  - Start testing #126 (export PDF)

Blockers:
  - Issue #127 không rõ AC, đang chờ PM clarify
```

### Sprint Planning

**QA tham gia:**
```
- Review AC của issues trong sprint
- Hỏi PM nếu AC chưa rõ
- Estimate test effort (nếu team track QA story points)
- Đảm bảo có đủ time test + regression
```

### Sprint Review

**QA demo:**
```
- Show tested features
- Report bugs found & fixed
- Metrics: bug count, pass rate, etc.
```

### Retrospective

**QA share:**
```
What went well:
  - Dev viết AC rõ ràng hơn

What can improve:
  - Issue #123 thiếu design → QA test sai hướng
  - Suggestion: Attach Figma link trong mọi feature issue
```

---

## ❓ FAQ

### **Q1: QA có nên approve PR không?**

**A**: **Nên**, nếu team setup workflow như vậy.
- QA review PR để hiểu code thay đổi gì
- QA không cần hiểu chi tiết code, nhưng cần biết scope
- QA approve = "I understand what changed, will test accordingly"

### **Q2: QA test xong, ai close issue?**

**A**: Tuỳ team, nhưng khuyến nghị:
- **QA comment "PASS"** → chuyển status "Done"
- **PM verify** → close issue
- (Hoặc auto-close khi deploy production)

### **Q3: Issue thiếu AC, QA có test được không?**

**A**: **KHÔNG**.
- QA comment: "@pm-bob Issue này thiếu AC, không test được"
- Update status: "Blocked"
- Đợi PM update AC
- Không test "theo cảm" vì sẽ sai

### **Q4: QA tìm bug khi test regression, có tạo issue không?**

**A**: **CÓ**.
- Tạo bug issue mới
- Mention trong issue đang test: "Found regression bug #125 while testing #123"
- PM quyết định: Fix ngay hay defer

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu vai trò QA trong GitHub Projects
- [ ] Hiểu workflow: Nhận issue → Đọc → Test → Report
- [ ] Biết QA test gì: Happy path, Edge case, Regression
- [ ] Biết cách comment result (PASS/FAIL)
- [ ] Biết khi nào tạo bug issue (xem file 06)
- [ ] Hiểu QA view trong Projects
- [ ] Hiểu anti-patterns phải tránh

---

**🚀 Tiếp theo: [06-bug-reporting-guide.md](./06-bug-reporting-guide.md) - Cách báo cáo Bug chuẩn**
