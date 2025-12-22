# 03 - PM Role & Workflow

> **Mục tiêu**: Hiểu vai trò PM trong GitHub Projects và quy trình làm việc chuẩn

**Thời lượng**: 60 phút
**Đối tượng**: PM, BA, Product Owner

---

## 🎯 Vai trò của PM trong GitHub Projects

### PM không chỉ là "người tạo issue"

PM trong GitHub Projects là:

- 🧭 **Navigator**: Dẫn dắt hướng đi của sản phẩm
- 📝 **Translator**: Dịch business requirement → technical issue
- 🔍 **Quality Gatekeeper**: Đảm bảo issue đủ rõ để Dev & QA làm việc
- 📊 **Progress Tracker**: Theo dõi tiến độ (không micromanage)
- 🤝 **Bridge**: Kết nối stakeholder ↔ Dev ↔ QA

### PM KHÔNG làm gì?

- ❌ Không code
- ❌ Không assign người làm (Dev tự pick)
- ❌ Không đổi status của issue đang dev làm
- ❌ Không close issue trước khi QA verify

---

## 🚀 Quy trình PM chuẩn trong GitHub Projects

### Big Picture: Từ ý tưởng → Production

```
┌──────────────┐
│   Ý TƯỞNG    │ (từ stakeholder, user feedback, data)
└──────┬───────┘
       ↓
┌──────────────┐
│ REQUIREMENT  │ (PM research, viết spec)
└──────┬───────┘
       ↓
┌──────────────┐
│   EPIC       │ (nhóm các tính năng lớn)
└──────┬───────┘
       ↓
┌──────────────┐
│   ISSUES     │ (PM chia nhỏ thành từng issue cụ thể)
└──────┬───────┘
       ↓
┌──────────────┐
│   SPRINT     │ (PM + Dev planning sprint)
└──────┬───────┘
       ↓
┌──────────────┐
│   DEV        │ (Dev code → PR → Review)
└──────┬───────┘
       ↓
┌──────────────┐
│   QA         │ (QA test → Pass/Fail)
└──────┬───────┘
       ↓
┌──────────────┐
│   RELEASE    │ (PM verify → Deploy → Monitor)
└──────────────┘
```

---

## 📋 Bước 1: Từ Ý tưởng → Requirement

### Input: Ý tưởng thô

Ví dụ:
> "Chúng ta cần cho phép user lưu sản phẩm để mua sau"

### PM cần làm gì?

#### 1. **Research & Validate**

```markdown
❓ Câu hỏi PM cần trả lời:
  - User nào cần tính năng này? (persona)
  - Tại sao họ cần? (pain point)
  - Họ dùng như thế nào? (user flow)
  - Có giải pháp thay thế nào không? (alternative)
  - Tác động đến business? (impact)
```

#### 2. **Viết Requirement (PRD light)**

```markdown
## Feature: Save for Later

### Problem
User thường add nhiều sản phẩm vào giỏ, nhưng chưa muốn mua ngay.
Nếu họ remove khỏi giỏ → mất thời gian tìm lại sau.

### Solution
Cho phép user "Save for Later" → sản phẩm chuyển sang tab riêng,
không mất khi user checkout các sản phẩm khác.

### User Flow
1. User có 5 sản phẩm trong giỏ
2. User click "Save for Later" ở sản phẩm #3
3. Sản phẩm #3 chuyển sang tab "Saved Items"
4. User checkout 4 sản phẩm còn lại
5. Sau 1 tuần, user mở tab "Saved Items" → vẫn thấy sản phẩm #3

### Success Metrics
- 20% user dùng "Save for Later" trong tháng đầu
- Conversion rate tăng 5% (vì giỏ hàng gọn hơn)

### Out of Scope (v1)
- Không có notification "giá giảm" cho saved items
- Không share saved items cho người khác
```

#### 3. **Sketch UI (optional nhưng nên có)**

Dùng Figma/Sketch hoặc vẽ tay → attach vào requirement.

---

## 🗂️ Bước 2: Từ Requirement → Epic

### Epic là gì?

**Epic** = Nhóm các tính năng lớn, thường span nhiều sprint.

**Lưu ý**: GitHub không có kiểu issue "Epic" native. PM thường dùng:
- **Option A**: Issue với label `epic`
- **Option B**: Custom field `Epic` (text field)
- **Option C**: Milestone (nếu epic có deadline rõ ràng)

### Ví dụ Epic Issue

```markdown
Issue #500: [EPIC] Save for Later Module

Labels: epic, feature, high-priority
Milestone: Q2 2024

---

## Overview
Cho phép user lưu sản phẩm để mua sau, không mất khi checkout.

## Related Issues (Children)
- [ ] #501: API - Add "save_for_later" field to cart_items
- [ ] #502: Backend - Create endpoint POST /api/cart/save-for-later
- [ ] #503: Backend - Create endpoint GET /api/saved-items
- [ ] #504: Frontend - Add "Save for Later" button UI
- [ ] #505: Frontend - Create "Saved Items" tab
- [ ] #506: QA - Test end-to-end flow
- [ ] #507: Docs - Update API documentation

Progress: 2/7 completed (29%)

## Timeline
- Sprint 20: Backend (#501, #502, #503)
- Sprint 21: Frontend (#504, #505)
- Sprint 22: QA + Polish (#506, #507)

## Success Criteria
- [ ] User có thể save item
- [ ] User có thể xem saved items
- [ ] Data persist sau khi logout/login lại
- [ ] Performance: API < 200ms
```

### ✅ Epic tốt vs ❌ Epic tệ

| ✅ Epic tốt | ❌ Epic tệ |
|-----------|----------|
| Có list child issues rõ ràng | Không chia nhỏ thành issues |
| Có success criteria đo được | Mục tiêu mơ hồ ("improve UX") |
| Có timeline ước lượng | Không có timeline |
| Update progress thường xuyên | Tạo xong bỏ không |

---

## 📝 Bước 3: Từ Epic → Issues (Chia nhỏ)

### Nguyên tắc chia Issue

#### 1. **Size phù hợp**

```
❌ Quá lớn: "Xây dựng module thanh toán" (3 tuần)
✅ Vừa phải: "Implement Stripe payment API" (2-3 ngày)

❌ Quá nhỏ: "Đổi màu button từ xanh sang đỏ" (5 phút)
✅ Gộp lại: "Update UI theo design mới" (1-2 ngày)
```

**Rule of thumb:**
- 1 issue = 0.5 - 3 ngày làm việc
- Nếu > 3 ngày → chia nhỏ hơn
- Nếu < 0.5 ngày → gộp với issue khác hoặc làm luôn không cần issue

#### 2. **Độc lập (INVEST principle)**

Issue nên:
- **I**ndependent: Làm được độc lập, không phụ thuộc nhiều issue khác
- **N**egotiable: Có thể thảo luận scope
- **V**aluable: Tạo giá trị cho user/business
- **E**stimable: Dev ước lượng được effort
- **S**mall: Đủ nhỏ để hoàn thành trong sprint
- **T**estable: QA test được

#### 3. **Rõ ràng (có Acceptance Criteria)**

**❌ Issue tệ:**

```markdown
Issue #123: Fix login bug

Description: Login bị lỗi
```

**✅ Issue tốt:**

```markdown
Issue #123: User không thể login bằng Google OAuth

Description:
User click "Login with Google" nhưng bị redirect về homepage
thay vì login thành công.

## Steps to Reproduce
1. Vào /login
2. Click "Login with Google"
3. Authorize Google account
4. Redirected về / (homepage)
5. User vẫn chưa login (check: cookie không có auth_token)

## Expected Behavior
User được login và redirect về /dashboard

## Actual Behavior
User bị redirect về / và vẫn chưa login

## Acceptance Criteria
- [ ] User click "Login with Google" → redirect đúng về /dashboard
- [ ] User đã login (có auth_token cookie)
- [ ] User thấy tên mình ở header
- [ ] Test trên Chrome, Firefox, Safari

## Technical Context
- Nghi ngờ: OAuth redirect_uri sai
- File liên quan: `auth/google-oauth.ts`
- Logs: "redirect_uri_mismatch" error

Environment: Staging (staging.myapp.com)
Priority: High (blocker for launch)
```

### Các loại Issue PM thường tạo

#### A. **Feature Issue**

Template:

```markdown
Issue #XXX: [Feature] <Tính năng>

Labels: feature, <priority>

## Description
<Mô tả tính năng, tại sao cần, user nào dùng>

## User Story
As a <user type>,
I want to <action>,
So that <benefit>.

## Acceptance Criteria
- [ ] <Điều kiện 1 để feature được coi là done>
- [ ] <Điều kiện 2>
- [ ] <Điều kiện 3>

## Design / Mockup
<Link Figma / Screenshot>

## Technical Notes (optional)
<Gợi ý technical nếu PM biết>

## Definition of Done
- [ ] Code completed
- [ ] PR merged
- [ ] QA tested & passed
- [ ] Deployed to production
```

#### B. **Task Issue (Technical)**

```markdown
Issue #XXX: [Task] <Công việc kỹ thuật>

Labels: task, tech-debt

## Description
<Tại sao cần làm task này>

## Checklist
- [ ] <Subtask 1>
- [ ] <Subtask 2>
- [ ] <Subtask 3>

## Expected Outcome
<Kết quả mong đợi>

## Definition of Done
- [ ] Task completed
- [ ] Code reviewed
- [ ] Merged to main
```

#### C. **Bug Issue**

(Xem chi tiết file 06-bug-reporting-guide.md)

PM thường **KHÔNG** tạo bug issue (QA tạo).
Nhưng PM cần review bug issue để:
- Xác định priority
- Quyết định fix ở sprint nào

---

## 📊 Bước 4: PM Quản lý Project Board

### Setup Project Board chuẩn

#### 1. **Tạo Project**

```
GitHub → Organization/Repo → Projects → New Project
  → Chọn "Board" template
  → Tên: "Product Development - Q2 2024"
```

#### 2. **Thiết lập Columns (Status)**

PM nên setup status như sau:

```
┌─────────┐  ┌──────┐  ┌─────────┐  ┌────────┐  ┌─────────┐  ┌──────┐
│ Backlog │→ │ Todo │→ │ In Prog │→ │ Review │→ │ Testing │→ │ Done │
└─────────┘  └──────┘  └─────────┘  └────────┘  └─────────┘  └──────┘
    ↓                                                              ↑
┌─────────┐                                                        │
│ Blocked │ ──────────────────────────────────────────────────────┘
└─────────┘
```

**Ý nghĩa từng column:**

| Status | Ai chịu trách nhiệm | Khi nào issue ở đây |
|--------|---------------------|---------------------|
| **Backlog** | PM | Issue đã validated nhưng chưa plan sprint |
| **Todo** | PM | Issue đã được plan cho sprint hiện tại |
| **In Progress** | Dev | Dev đang code |
| **Review** | Dev + Reviewer | PR đã tạo, chờ code review |
| **Testing** | QA | Code đã merge, QA đang test |
| **Blocked** | Dev/QA | Bị kẹt (thiếu API, chờ design, etc.) |
| **Done** | - | QA test pass + deployed |

#### 3. **Thêm Custom Fields**

PM nên tạo các field sau:

| Field Name | Type | Values | Mục đích |
|------------|------|--------|----------|
| **Priority** | Select | Critical, High, Medium, Low | Độ ưu tiên |
| **Estimation** | Number | 1, 2, 3, 5, 8, 13 (Fibonacci) | Story points |
| **Epic** | Text | "User Auth", "Payment", etc. | Nhóm feature |
| **Sprint** | Iteration | Sprint 20, Sprint 21, ... | Gắn sprint |
| **QA Status** | Select | Not Started, In Progress, Passed, Failed | Trạng thái test |

#### 4. **Tạo Views khác nhau**

**View 1: Board (Daily standup)**

```
Filter: Sprint = "Sprint 20"
Group by: Status
Sort: Priority (High → Low)
```

**View 2: PM Planning Table**

```
Columns: Title, Status, Priority, Estimation, Assignee, Epic
Filter: Status != Done
Sort: Priority DESC
```

**View 3: Roadmap (Stakeholder)**

```
View type: Roadmap
X-axis: Date
Group by: Epic
Filter: Priority = High or Critical
```

---

## 📅 Bước 5: Sprint Planning (PM + Dev)

### Quy trình Sprint Planning

#### **Trước meeting (PM chuẩn bị)**

```markdown
PM Checklist trước Sprint Planning:
  - [ ] Review backlog, ưu tiên issues theo business value
  - [ ] Đảm bảo top issues có đủ Acceptance Criteria
  - [ ] Estimate story points (với Dev lead nếu cần)
  - [ ] Chuẩn bị demo mockup/design cho issues mới
  - [ ] Check capacity team (ai nghỉ, ai onboard mới)
```

#### **Trong meeting (60-90 phút)**

**Phần 1: Review sprint trước (15 phút)**

```
- Demo features đã done
- Retrospective: gì tốt, gì cần cải thiện
```

**Phần 2: Planning sprint mới (45 phút)**

```
1. PM present top priority issues (theo thứ tự business value)
2. Dev hỏi clarification
3. Dev estimate story points
4. PM + Dev chọn issues vào sprint (dựa trên capacity)
5. Dev tự assign issues (không phải PM assign)
```

**Phần 3: Confirm & Commit (15 phút)**

```
- Confirm sprint goal
- Confirm issues in sprint
- Confirm Definition of Done
```

#### **Sau meeting (PM)**

```markdown
PM Checklist sau Sprint Planning:
  - [ ] Update all issues với Sprint field = "Sprint X"
  - [ ] Move issues từ Backlog → Todo
  - [ ] Share sprint goal trong Slack/Team chat
  - [ ] Update roadmap view cho stakeholder
```

---

## 📈 Bước 6: Theo dõi tiến độ (Daily)

### PM không nên micromanage

**❌ PM không nên:**
- Hỏi dev "xong chưa" mỗi 2 tiếng
- Đổi status issue thay dev
- Assign issue cho dev mà không hỏi

**✅ PM nên:**
- Check project board 1-2 lần/ngày
- Quan tâm **issue bị kẹt** (ở Blocked > 1 ngày)
- Hỏi "Em cần gì để unblock?" thay vì "Khi nào xong?"

### Dashboard PM nên xem hàng ngày

#### **View 1: Sprint Health**

```
Filter: Sprint = "Current Sprint"
Group by: Status

Câu hỏi PM tự hỏi:
  ✓ Có issue nào ở "In Progress" quá 3 ngày?
    → Có thể quá lớn, cần chia nhỏ?
  ✓ Có issue nào ở "Blocked"?
    → PM cần giúp unblock gì không?
  ✓ "Todo" còn bao nhiêu issue?
    → Có kịp sprint không?
```

#### **View 2: Issue cần attention**

```
Filter:
  - Status = Blocked OR
  - (Status = In Progress AND Updated > 3 days ago) OR
  - (Status = Review AND Updated > 1 day ago)

→ Đây là những issue cần PM check
```

#### **View 3: QA Backlog**

```
Filter: Status = Testing
Group by: QA Status

→ PM check xem QA có quá tải không
```

---

## 🎯 Bước 7: Issue Close & Release

### Khi nào issue được close?

```
Flow đóng issue:

Dev code xong → tạo PR → link vào issue
  ↓
PR được merge → Dev comment trong issue "Merged, ready for QA"
  ↓
QA test → comment "✅ Tested on staging, PASS"
  ↓
PM verify trên staging → comment "✅ Verified, ready for release"
  ↓
Deploy to production
  ↓
PM close issue + add comment "Released in v1.5.0"
```

**⚠️ Lưu ý:**
- Issue **CHỈ được close** khi đã deployed production (hoặc stakeholder đồng ý không deploy)
- Không close issue chỉ vì code đã merge

### Release Checklist cho PM

```markdown
Pre-release Checklist:
  - [ ] Tất cả issues trong milestone đã ở status "Done"
  - [ ] QA đã test regression
  - [ ] PM đã verify trên staging
  - [ ] Release notes đã viết
  - [ ] Stakeholder đã review & approve
  - [ ] Rollback plan đã chuẩn bị

Post-release Checklist:
  - [ ] Deploy thành công
  - [ ] Smoke test trên production
  - [ ] Monitor logs/errors trong 1-2 giờ
  - [ ] Close milestone
  - [ ] Announce release to team & stakeholders
```

---

## 🧠 PM Best Practices

### ✅ Làm đúng

#### 1. **Viết Issue rõ ràng**

```
Mỗi issue phải trả lời được 3 câu hỏi:
  1. Làm GÌ? (What) → Description
  2. Tại SAO? (Why) → Context / User Story
  3. Thế NÀO là xong? (Done) → Acceptance Criteria
```

#### 2. **Single Source of Truth**

```
❌ Sai:
  - Spec trong Google Doc
  - Issue trong GitHub
  - Discussion trong Slack
  → 3 nơi không sync

✅ Đúng:
  - Issue = source of truth
  - Google Doc → link trong issue description
  - Slack discussion → tóm tắt lại trong issue comment
```

#### 3. **Keep issues updated**

```
PM nên:
  - Update progress trong issue comment
  - Link related issues (duplicate, blocker, related)
  - Close stale issues (> 3 tháng không activity)
```

#### 4. **Data-driven prioritization**

```
PM prioritize dựa trên:
  - Business value (revenue, retention, acquisition)
  - User impact (số user affected)
  - Effort (story points, complexity)
  - Dependencies (blocker cho issue khác)

Công thức: Priority = (Business Value × User Impact) / Effort
```

#### 5. **Communicate proactively**

```
PM nên update stakeholder:
  - Daily: Sprint progress (qua dashboard, không cần meeting)
  - Weekly: Sprint review summary
  - Bi-weekly: Roadmap update
  - Monthly: Metrics & OKR review
```

---

### ❌ Làm sai (Anti-patterns)

#### 1. **Issue quá mơ hồ**

```markdown
❌ Issue tệ:
  Title: "Improve UX"
  Description: "User feedback says UX is bad"

→ Dev không biết làm gì, QA không biết test gì
```

#### 2. **Thay đổi scope giữa sprint**

```
❌ Sai:
  Dev đang code issue #123
  PM thêm yêu cầu mới vào issue #123
  → Dev phải làm lại, sprint delay

✅ Đúng:
  PM tạo issue mới #124 với yêu cầu mới
  Plan vào sprint sau
```

#### 3. **Không link issue ↔ PR**

```
❌ Sai:
  Dev tạo PR nhưng không mention issue
  → PM/QA không biết PR này fix issue nào

✅ Đúng:
  PR description có:
    "Closes #123"
  → GitHub auto link PR ↔ Issue
```

#### 4. **Close issue quá sớm**

```
❌ Sai:
  Dev: "Code xong rồi" → close issue
  QA: "Ơ, tôi chưa test?"

✅ Đúng:
  Dev merge PR → comment "Ready for QA"
  QA test pass → comment "✅ PASS"
  PM verify → close issue
```

---

## 📊 Metrics PM nên theo dõi

### Sprint-level Metrics

| Metric | Cách đo | Target |
|--------|---------|--------|
| **Velocity** | Tổng story points completed/sprint | Stable (±20%) |
| **Sprint Completion Rate** | % issues Done / Total issues | > 80% |
| **Carry-over Rate** | % issues không done chuyển sprint sau | < 20% |
| **Blocked Rate** | % thời gian issues ở status Blocked | < 10% |

### Quality Metrics

| Metric | Cách đo | Target |
|--------|---------|--------|
| **Bug Escape Rate** | % bugs found in production / total bugs | < 10% |
| **Cycle Time** | Thời gian từ Todo → Done | < 5 ngày |
| **PR Review Time** | Thời gian từ PR tạo → merge | < 1 ngày |
| **QA Pass Rate** | % issues pass QA lần đầu | > 80% |

### Cách track metrics trong GitHub Projects

```markdown
Weekly PM Review:
  1. Export Table view → CSV
  2. Tính metrics trong Excel/Google Sheets
  3. Vẽ chart
  4. Share với team

(Hoặc dùng GitHub Insights nếu có)
```

---

## ❓ FAQ

### **Q1: PM có nên assign issue cho dev không?**

**A**: **Không nên**.
- Trong Agile, dev tự pick issue (pull model)
- PM chỉ prioritize issue trong backlog
- Dev pick theo capacity và skill của mình

**Trường hợp đặc biệt**: Issue critical, cần người specific → PM hỏi dev trước khi assign.

### **Q2: PM có nên estimate story points không?**

**A**: **Không, dev estimate**.
- Dev hiểu technical complexity hơn PM
- PM có thể suggest business priority
- Estimate là team activity (planning poker)

### **Q3: Issue bị kẹt quá lâu, PM làm gì?**

**A**: PM giúp unblock:
1. Hỏi dev: "Cần gì để unblock?"
2. Nếu thiếu design → escalate cho designer
3. Nếu thiếu API → escalate cho backend team
4. Nếu scope không rõ → clarify ngay

**Đừng blame dev**, hãy help remove blocker.

### **Q4: Stakeholder muốn thêm feature giữa sprint, PM làm sao?**

**A**:
1. Tạo issue mới với priority HIGH
2. Thảo luận với team:
   - Option A: Thêm vào sprint (nếu còn capacity)
   - Option B: Swap với issue priority thấp hơn
   - Option C: Plan vào sprint sau
3. **Không** thêm trực tiếp mà không hỏi team

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu vai trò PM trong GitHub Projects
- [ ] Hiểu quy trình: Ý tưởng → Requirement → Epic → Issue → Sprint → Release
- [ ] Biết cách chia Epic thành Issues
- [ ] Biết cách viết Issue tốt (xem file 04 để practice)
- [ ] Biết cách setup Project Board
- [ ] Hiểu PM tracking progress như thế nào
- [ ] Hiểu anti-patterns phải tránh

---

**🚀 Tiếp theo: [04-issue-writing-guide.md](./04-issue-writing-guide.md) - Chi tiết cách viết Issue chuẩn**
