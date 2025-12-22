# 10 - Practical Exercises (Bài tập thực hành)

> **Mục tiêu**: Thực hành kỹ năng đã học qua bài tập thực tế

**Thời lượng**: 2-4 giờ
**Đối tượng**: PM, QA, Dev (tất cả)

---

## 🎯 Hướng dẫn sử dụng

### Cách làm bài tập

1. **Chuẩn bị**: Tạo test repository hoặc dùng repository thật
2. **Làm bài**: Theo hướng dẫn từng bước
3. **Self-review**: Dùng checklist để tự đánh giá
4. **Peer review**: Nhờ đồng nghiệp review (nếu có)

---

## 📝 Bài tập cho PM

### Bài tập 1: Tạo Epic và chia Issue

**Đề bài:**

> Stakeholder yêu cầu: "Chúng ta cần thêm tính năng Dark Mode cho app"

**Nhiệm vụ:**

1. Tạo Epic Issue cho Dark Mode
2. Chia Epic thành 5-7 issues nhỏ
3. Viết AC cho từng issue
4. Estimate story points (giả định)
5. Setup project board với issues này

**Bước thực hiện:**

```markdown
Bước 1: Tạo Epic Issue
  Title: [EPIC] Dark Mode Support
  Description:
    - Problem statement
    - User story
    - List child issues
    - Timeline
  Labels: epic, feature
  Milestone: Q2 2024

Bước 2: Brainstorm child issues
  - Backend: User preference API
  - Frontend: Dark theme CSS
  - Frontend: Toggle button UI
  - Frontend: Persist user preference
  - QA: Test all screens
  - Docs: Update user guide

Bước 3: Tạo từng issue với AC đầy đủ
  (Dùng template trong file 04)

Bước 4: Link issues với epic
  Epic issue #500
    - Child: #501, #502, #503, #504, #505, #506

Bước 5: Setup project board
  - Add all issues vào project
  - Set priority
  - Set estimation
  - Set sprint (if applicable)
```

**Checklist tự đánh giá:**

```markdown
- [ ] Epic issue có đủ: Problem, Solution, Child issues, Timeline
- [ ] Mỗi child issue có: Title rõ ràng, AC, DoD
- [ ] Issues có size hợp lý (1-3 ngày mỗi issue)
- [ ] Issues độc lập, có thể làm song song
- [ ] Labels, priority đúng
- [ ] Link epic ↔ child issues
```

---

### Bài tập 2: Sprint Planning Simulation

**Đề bài:**

> Team capacity: 40 points (2 dev, 1 QA, 2 tuần)
> Backlog: 10 issues với priorities và estimates
> Plan sprint 16

**Nhiệm vụ:**

1. Review backlog, chọn issues vào sprint
2. Ensure không overcommit (< capacity)
3. Viết sprint goal
4. Update issues với sprint field

**Issues (giả định):**

```
Backlog:
  #101: [Feature] Export PDF (P0, 13 points)
  #102: [Feature] Dark mode toggle (P0, 8 points)
  #103: [Bug] Login fails Safari (P0, 5 points)
  #104: [Feature] User analytics (P1, 13 points)
  #105: [Bug] Typo in header (P2, 1 point)
  #106: [Task] Refactor auth (P1, 8 points)
  #107: [Feature] Notification center (P1, 21 points)
  #108: [Bug] Slow query (P1, 5 points)
  #109: [Docs] API documentation (P2, 3 points)
  #110: [Feature] Share to social (P2, 8 points)
```

**Solution:**

```markdown
Sprint 16 Plan (40 points capacity):

Selected issues (38 points):
  ✓ #101: Export PDF (P0, 13 pts)
  ✓ #102: Dark mode (P0, 8 pts)
  ✓ #103: Login bug Safari (P0, 5 pts)
  ✓ #108: Slow query (P1, 5 pts)
  ✓ #106: Refactor auth (P1, 8 pts)
  Total: 39 points (slight over, but acceptable)

NOT selected:
  ✗ #104: User analytics (13 pts) - would overcommit
  ✗ #107: Too large (21 pts) - chia nhỏ hơn
  ✗ #109, #110: Lower priority

Sprint Goal:
  "Ship Export PDF + Dark Mode to production
   Fix P0 bugs + Tech debt (auth refactor)"

Actions:
  - Update all selected issues: Sprint = "Sprint 16"
  - Move: Backlog → Todo
  - #107: Create subtasks để fit sprint sau
```

**Checklist:**

```markdown
- [ ] Không overcommit (selected < capacity)
- [ ] Prioritize đúng (P0 trước)
- [ ] Sprint goal rõ ràng
- [ ] Issues updated với sprint field
```

---

## 🧪 Bài tập cho QA

### Bài tập 3: Viết Test Plan

**Đề bài:**

> Feature: User có thể "Save for Later" trong giỏ hàng
> AC:
>   - User click "Save for Later" → item chuyển sang tab "Saved Items"
>   - Saved items persist sau logout/login
>   - User có thể "Move to Cart" để chuyển lại

**Nhiệm vụ:**

1. Viết test plan với ít nhất 8 test cases
2. Cover: Happy path, Edge cases, Error cases
3. Specify: Precondition, Steps, Expected result

**Template:**

```markdown
## Test Plan: Save for Later Feature

### Test Scope
- Save item functionality
- View saved items
- Move back to cart
- Data persistence

### Test Cases

#### TC-001: Save item from cart
- **Precondition**: User logged in, 3 items in cart
- **Steps**:
  1. Click "Save for Later" on item #1
  2. Observe cart
  3. Open "Saved Items" tab
- **Expected**:
  - Cart: 2 items (item #1 removed)
  - Saved Items: 1 item (item #1)

#### TC-002: Saved items persist after logout
- **Precondition**: User has 1 saved item
- **Steps**:
  1. Logout
  2. Login lại same account
  3. Open "Saved Items" tab
- **Expected**:
  - Saved item vẫn hiển thị

#### TC-003: Move saved item back to cart
- **Precondition**: User has 1 saved item, cart empty
- **Steps**:
  1. Open "Saved Items"
  2. Click "Move to Cart" on saved item
  3. Check cart
- **Expected**:
  - Saved Items: empty
  - Cart: 1 item

#### TC-004: Save multiple items
(Bạn viết tiếp...)

#### TC-005: Save 100 items (edge case)
(Bạn viết tiếp...)

#### TC-006: Saved item, product deleted (edge case)
(Bạn viết tiếp...)

#### TC-007: API timeout (error case)
(Bạn viết tiếp...)

#### TC-008: Concurrent save from 2 tabs (edge case)
(Bạn viết tiếp...)
```

**Checklist:**

```markdown
- [ ] Có ít nhất 8 test cases
- [ ] Cover happy path (TC-001, 002, 003)
- [ ] Cover edge cases (TC-004, 005, 006, 008)
- [ ] Cover error cases (TC-007)
- [ ] Mỗi TC có: Precondition, Steps, Expected
```

---

### Bài tập 4: Report Bug chuẩn

**Đề bài:**

> Khi test TC-002 (Persist after logout), QA phát hiện:
> Saved items KHÔNG persist sau logout.

**Nhiệm vụ:**

Viết bug report hoàn chỉnh theo template file 06.

**Solution:**

```markdown
Issue #125: [Bug] Saved items không persist sau logout

## Description
User save item, logout, login lại → Saved items tab empty.
Data không được lưu vào database hoặc không load lại.

**Impact:**
- Core functionality broken
- 100% users affected
- Data loss

## Steps to Reproduce
1. Login as test@example.com
2. Add 3 items to cart (Product #101, #102, #103)
3. Click "Save for Later" on item #101
4. Verify: Saved Items tab có item #101 ✓
5. Logout
6. Login lại cùng account
7. Open "Saved Items" tab

**Result:** Tab empty (expected: có item #101)

## Expected Behavior
Saved items vẫn hiển thị sau logout/login

## Actual Behavior
Saved items tab empty

## Environment
- URL: https://staging.myapp.com
- Browser: Chrome 120 (macOS 14)
- Account: test@example.com

## Screenshots
(Attached: saved_items_empty.png)

## Logs
Console:
```
GET /api/saved-items
Response: { items: [] }
```

Expected response: { items: [{id: 101, ...}] }

## Frequency
Always (100%)

## Severity
**High** - Core functionality không work

## Root Cause (Suspected)
Backend không save vào database?
Hoặc Frontend không call API load saved items?

## Related Issue
#123 (Save for Later feature)

---

**Labels**: bug, high-priority, team:backend
**Priority**: High
**Assignee**: @dev-backend
```

**Checklist:**

```markdown
- [ ] Steps to reproduce rõ ràng (1, 2, 3...)
- [ ] Expected vs Actual
- [ ] Environment đầy đủ
- [ ] Screenshot/logs
- [ ] Severity đúng
```

---

## 👥 Bài tập nhóm (PM + Dev + QA)

### Bài tập 5: Sprint Simulation

**Đề bài:**

> Simulate 1 sprint đầy đủ với team 3 người (PM, Dev, QA)

**Roles:**

- **PM**: @student-1
- **Dev**: @student-2
- **QA**: @student-3

**Timeline:** 3-4 giờ

**Nhiệm vụ:**

```
Hour 1: Sprint Planning (60 min)
  - PM: Present 5 issues từ backlog
  - Dev: Estimate, ask questions
  - QA: Review AC
  - Team: Select 3 issues cho sprint
  - Output: Sprint board với 3 issues

Hour 2: Development (60 min)
  - Dev: Pick issue #1, code (hoặc mock code)
  - Dev: Create PR (hoặc mock PR)
  - QA: Review PR, plan test
  - PM: Monitor, answer questions

Hour 3: Testing (60 min)
  - Dev: "Merge" PR, notify QA
  - QA: Test issue #1
  - QA: Report result (PASS or FAIL)
  - Dev: Fix bugs if FAIL
  - PM: Verify

Hour 4: Sprint Review & Retro (30 min)
  - Team: Demo issue done
  - Team: Retrospective (Start-Stop-Continue)
  - Team: Write lessons learned
```

**Setup:**

```markdown
Tạo test repo:
  - Create 5 issues (simple features/bugs)
  - Create project board
  - Assign roles

Example issues:
  #1: [Feature] Add "Like" button
  #2: [Bug] Button color wrong
  #3: [Task] Update README
  #4: [Feature] Add search bar
  #5: [Bug] Typo in footer
```

**Deliverables:**

```markdown
- [ ] Sprint board với 3 issues selected
- [ ] Ít nhất 1 issue "Done" (end-to-end)
- [ ] 1 bug report (nếu QA tìm thấy bug)
- [ ] Retrospective notes
```

**Lessons to learn:**

- PM: Cách viết issue rõ ràng
- Dev: Cách communicate progress
- QA: Cách test & report
- Team: Cách collaborate trên GitHub

---

## 🎓 Bài tập nâng cao

### Bài tập 6: Migration từ Jira/Trello

**Đề bài:**

> Team đang dùng Trello, muốn migrate sang GitHub Projects

**Nhiệm vụ:**

1. Export data từ Trello (CSV)
2. Map Trello → GitHub concepts
3. Import vào GitHub Projects
4. Setup workflow tương đương

**Mapping:**

```
Trello → GitHub Projects

Card → Issue
List → Status (column)
Label → Label
Member → Assignee
Checklist → Subtasks in issue body
Due Date → Milestone or custom field
```

**Steps:**

```
1. Export Trello board → CSV
2. Transform CSV:
   - Card Title → Issue Title
   - Card Description → Issue Description
   - List name → Status
3. Create GitHub issues (bulk import hoặc script)
4. Setup GitHub Project board với statuses
5. Import issues vào project
```

**Tools:**

- Trello export: Board → Menu → Print/Export → JSON
- GitHub import: Issue templates, or GitHub CLI

---

### Bài tập 7: Automation với GitHub Actions

**Đề bài:**

> Automate: Khi PR merge → auto update issue status "Testing"

**Nhiệm vụ:**

Viết GitHub Action workflow

**Solution:**

```yaml
# .github/workflows/auto-update-status.yml

name: Auto Update Issue Status

on:
  pull_request:
    types: [closed]

jobs:
  update-status:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
      - name: Update linked issues
        uses: actions/github-script@v6
        with:
          script: |
            const pr = context.payload.pull_request;
            const body = pr.body || '';

            // Extract issue numbers from PR body
            const issueNumbers = body.match(/#(\d+)/g);

            if (issueNumbers) {
              for (const num of issueNumbers) {
                const issueNumber = num.replace('#', '');

                // Comment on issue
                await github.rest.issues.createComment({
                  owner: context.repo.owner,
                  repo: context.repo.repo,
                  issue_number: issueNumber,
                  body: `✅ PR #${pr.number} merged. Ready for QA testing.\n\n@qa-team please test.`
                });
              }
            }
```

---

## ✅ Checklist hoàn thành

### PM

```markdown
- [ ] Làm xong bài tập 1: Tạo Epic
- [ ] Làm xong bài tập 2: Sprint Planning
- [ ] Tham gia bài tập 5: Sprint Simulation
```

### QA

```markdown
- [ ] Làm xong bài tập 3: Viết Test Plan
- [ ] Làm xong bài tập 4: Report Bug
- [ ] Tham gia bài tập 5: Sprint Simulation
```

### Team

```markdown
- [ ] Làm xong bài tập 5: Sprint Simulation (team)
- [ ] (Optional) Bài tập 6: Migration
- [ ] (Optional) Bài tập 7: Automation
```

---

**🚀 Tiếp theo: [12-anti-patterns.md](./12-anti-patterns.md) - Các lỗi thường gặp & cách tránh**
