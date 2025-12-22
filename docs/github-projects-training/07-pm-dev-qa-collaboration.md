# 07 - PM-Dev-QA Collaboration (Phối hợp PM-Dev-QA)

> **Mục tiêu**: Hiểu cách PM-Dev-QA làm việc cùng nhau trên GitHub Projects

**Thời lượng**: 60 phút
**Đối tượng**: PM, Dev, QA (tất cả)

---

## 🎯 Tại sao cần phối hợp tốt?

### Vấn đề khi không phối hợp

```
❌ PM tạo issue mơ hồ
   → Dev code sai hướng
   → QA test fail
   → Làm lại 3 lần
   → Waste 1 tuần

❌ Dev merge code không notify QA
   → QA không biết test
   → Bug lọt production
   → User complain

❌ QA report bug không rõ ràng
   → Dev không replicate được
   → Hỏi lại QA
   → QA đã quên (1 tuần trước)
```

### Khi phối hợp tốt

```
✅ PM viết issue rõ ràng
   → Dev hiểu ngay
   → Code đúng lần đầu
   → QA test pass
   → Release on time

✅ Dev mention QA khi merge PR
   → QA test ngay
   → Tìm bug sớm
   → Fix nhanh

✅ QA report bug chi tiết
   → Dev fix 1 lần xong
   → QA retest pass
   → Không mất thời gian hỏi lại
```

**Kết quả:**
- Cycle time giảm 50%
- Bug rate giảm 60%
- Team happiness tăng ⬆️

---

## 🔄 Issue Lifecycle trong GitHub Projects

### Trạng thái issue đi qua các vai trò

```
┌───────────────────────────────────────────────────────────────┐
│                       ISSUE LIFECYCLE                          │
├───────────┬─────────────┬─────────────┬──────────┬───────────┤
│  Backlog  │    Todo     │ In Progress │  Review  │  Testing  │ Done
├───────────┼─────────────┼─────────────┼──────────┼───────────┤
│           │             │             │          │           │
│   PM      │   PM+Dev    │     Dev     │   Dev    │    QA     │ PM+QA
│  tạo      │  planning   │   coding    │  review  │  testing  │ verify
│  issue    │   sprint    │             │   code   │           │
│           │             │             │          │           │
└───────────┴─────────────┴─────────────┴──────────┴───────────┘

Ai chịu trách nhiệm status:
  ✓ Backlog → Todo: PM (sprint planning)
  ✓ Todo → In Progress: Dev (pick issue)
  ✓ In Progress → Review: Dev (create PR)
  ✓ Review → Testing: Dev (merge PR, mention QA)
  ✓ Testing → Done: QA (test pass)
  ✓ Testing → In Progress: QA (test fail, create bug)
```

---

## 👥 Ai làm gì trong mỗi giai đoạn?

### Giai đoạn 1: Issue Creation (PM)

**PM:**
- ✅ Tạo issue với AC rõ ràng
- ✅ Attach design/mockup
- ✅ Add labels, priority, milestone
- ✅ Link epic (nếu có)

**Dev:**
- 👀 Review issue trong backlog
- 💬 Comment hỏi clarification nếu chưa rõ

**QA:**
- 👀 Review AC: có test được không?
- 💬 Suggest thêm test scenarios

**Ví dụ comment:**

```markdown
Issue #123: [Feature] Save for Later

@pm-bob (PM):
  Created issue with AC + design
  @dev-team please review, any questions?

@dev-alice (Dev):
  AC rõ ràng ✓
  Question: Saved items có expire sau X ngày không?

@pm-bob (PM):
  Good question! Không expire, lưu vĩnh viễn.
  (Updated AC)

@qa-carol (QA):
  Suggest thêm test case:
    - User save 100 items → pagination?
    - Product deleted → hiển thị gì?

@pm-bob (PM):
  Good catch! Added to AC ✓
```

---

### Giai đoạn 2: Sprint Planning (PM + Dev + QA)

**PM:**
- ✅ Present top priority issues
- ✅ Giải thích business context
- ✅ Answer questions

**Dev:**
- ✅ Estimate effort (story points)
- ✅ Hỏi clarification
- ✅ Commit issues vào sprint

**QA:**
- ✅ Review AC: đủ rõ để test?
- ✅ Estimate test effort
- ✅ Raise concerns sớm

**Ví dụ planning conversation:**

```
PM: "Issue #123 - Save for Later, business priority HIGH"
Dev: "Estimate 5 points, cần 2-3 ngày"
QA: "AC rõ ràng, estimate 1 ngày test + regression"
PM: "OK, add vào sprint ✓"
```

---

### Giai đoạn 3: Development (Dev)

**Dev:**
- ✅ Pick issue từ Todo (tự assign)
- ✅ Update status: Todo → In Progress
- ✅ Code + commit (reference issue: `#123` in commit)
- ✅ Create PR (link issue: `Closes #123` in PR body)
- ✅ Request review

**PM:**
- 👀 Monitor progress (không micromanage)
- 💬 Answer questions nếu Dev hỏi
- ❌ KHÔNG đổi status thay Dev

**QA:**
- 👀 Review PR (understand code changes)
- 💬 Comment test scenarios sẽ test

**Ví dụ PR:**

```markdown
PR #124: Add Save for Later functionality

**Closes #123**

## Changes
- Added API: POST /api/cart/save-for-later
- Added database migration: saved_for_later_at column
- Added UI: "Save for Later" button + "Saved Items" tab

## Testing
- Unit tests added
- Manual test: works on staging

@qa-carol Please test when merged ✓

---

Comments:
@qa-carol (QA):
  Reviewed PR ✓
  Will test:
    - Happy path
    - Persistence after logout
    - Edge: 100 items
  ETA: 2h after merge
```

---

### Giai đoạn 4: Code Review (Dev)

**Dev (Reviewer):**
- ✅ Review code logic, style, tests
- ✅ Approve hoặc Request Changes
- ✅ Merge PR (sau khi approve)

**PM:**
- 👀 Optional: đọc PR để hiểu implementation

**QA:**
- ✅ Review PR để hiểu scope thay đổi
- ✅ Plan test cases dựa trên code changes

---

### Giai đoạn 5: Testing (QA)

**QA:**
- ✅ Test theo AC
- ✅ Test edge cases, regression
- ✅ Comment result: PASS or FAIL
- ✅ Update status:
  - PASS → Testing → Done
  - FAIL → Testing → In Progress + create bug issue

**Dev:**
- 👀 Đợi QA test result
- 💬 Answer questions nếu QA hỏi
- 🐛 Fix bugs nếu QA tìm thấy

**PM:**
- 👀 Monitor QA progress
- ✅ Verify feature trên staging (optional)

**Ví dụ QA comment:**

```markdown
Issue #123: Save for Later

@qa-carol (QA):
  **Test Result: ❌ FAIL**

  Tested: 2024-06-15 14:00
  Environment: staging

  ✅ TC-001: Save item → PASS
  ✅ TC-002: View saved items → PASS
  ❌ TC-003: Persist after logout → FAIL

  Bug: Items KHÔNG persist
  Created bug issue #125
  Reassigning to @dev-alice

@dev-alice (Dev):
  Thanks! Will check #125

@dev-alice (Dev):
  Fixed in PR #126, merged to staging
  @qa-carol please retest

@qa-carol (QA):
  **Retest Result: ✅ PASS**
  All test cases passed ✓
  Moving to Done
```

---

### Giai đoạn 6: Done & Release (PM + QA)

**PM:**
- ✅ Verify feature trên staging
- ✅ Close issue
- ✅ Add to release notes

**QA:**
- ✅ Final smoke test trên staging
- ✅ Verify no regression

**Dev:**
- ✅ Monitor after deploy production
- 🐛 Hotfix nếu có issue

---

## 🤝 Best Practices cho Collaboration

### 1. Communication trong Issue Comments

**✅ Làm đúng:**

```markdown
# Mention người cần action
@dev-alice Please check this

# Rõ ràng, cụ thể
Expected: Button màu xanh (#007bff)
Actual: Button màu xanh đậm (#0056b3)

# Friendly tone
Thanks for the fix! ✓
```

**❌ Làm sai:**

```markdown
# Không mention
Someone fix this

# Mơ hồ
This is wrong

# Rude
Why did you do this? 😠
```

---

### 2. Linking Issues & PRs

**✅ Luôn luôn link:**

**PR body:**
```markdown
Closes #123
Fixes #125
Related to #100
```

**Issue comment:**
```markdown
Duplicate of #99
Blocked by #88
Depends on #77
```

**Benefit:**
- GitHub auto-link
- Trace requirement → code → bug dễ dàng
- Ai cũng thấy context

---

### 3. Status Update proactively

**✅ Dev nên:**

```markdown
# Khi pick issue
Comment: "Working on this, ETA 2 days"
Status: Todo → In Progress

# Khi tạo PR
Comment: "PR #124 ready for review"
Status: In Progress → Review

# Khi merge
Comment: "@qa-carol merged, please test"
Status: Review → Testing
```

**✅ QA nên:**

```markdown
# Khi bắt đầu test
Comment: "Testing now, ETA 2h"

# Khi test xong
Comment: "✅ PASS - all test cases passed"
Status: Testing → Done

# Nếu fail
Comment: "❌ FAIL - bug #125 created"
Status: Testing → In Progress
```

---

### 4. Daily Standup sử dụng GitHub Projects

**Format standup:**

```
Team nhìn vào Project Board (không cần prepare slides)

Dev:
  - Yesterday: Worked on #123 → PR #124 merged
  - Today: Will work on #126
  - Blockers: None

QA:
  - Yesterday: Tested #123 → FAIL, created bug #125
  - Today: Retest #123, start testing #126
  - Blockers: Issue #127 chưa rõ AC

PM:
  - Yesterday: Clarified #127, planning #128
  - Today: Review bugs, prioritize sprint
  - Blockers: Waiting design for #129
```

---

## 🔔 Notification & Mention Best Practices

### Khi nào nên mention?

**✅ Mention khi:**

- Dev tạo PR → mention QA: "@qa-alice please test"
- QA tìm bug → mention Dev: "@dev-bob please check"
- PM clarify requirement → mention Dev/QA: "@dev-alice updated AC"
- Blocker cần unblock → mention người có thể help

**❌ Đừng mention khi:**

- Spam mention mọi người: "@everyone check this"
- Mention không liên quan: "@random-person FYI"

---

### Setup GitHub Notifications

**Recommended settings:**

```
GitHub → Settings → Notifications

✅ Enable:
  - Issues assigned to you
  - Issues you're mentioned in
  - Pull requests you're mentioned in
  - Comments on issues you're watching

❌ Disable (tránh spam):
  - All activity in repositories
```

---

## 📊 Metrics theo dõi Collaboration Quality

### Team Metrics

| Metric | Cách đo | Target | Ý nghĩa |
|--------|---------|--------|---------|
| **Issue Clarification Rate** | % issues PM phải clarify sau khi tạo | < 20% | AC rõ ràng ngay từ đầu |
| **PR Review Time** | Thời gian từ PR tạo → merge | < 4 giờ | Review nhanh |
| **QA Cycle Time** | Thời gian từ PR merge → QA test done | < 1 ngày | QA test nhanh |
| **Bug Reopen Rate** | % bugs phải reopen (fix không xong) | < 10% | Dev fix tốt lần đầu |
| **Issue Comment Count** | Avg comments/issue | 3-7 | Collaboration đủ, không quá nhiều |

---

## ⚠️ Anti-patterns phải tránh

### ❌ Anti-pattern 1: PM micromanage

```
❌ PM:
  - Hỏi dev mỗi 2 tiếng: "Xong chưa?"
  - Đổi status thay dev
  - Assign issue cho dev mà không hỏi

✅ PM nên:
  - Trust dev estimate
  - Check board 1-2 lần/ngày
  - Hỏi nếu issue ở In Progress > 3 ngày: "Cần help gì không?"
```

---

### ❌ Anti-pattern 2: Dev không communicate

```
❌ Dev:
  - Code xong, merge, không notify QA
  - Issue unclear, không hỏi PM
  - Stuck 2 ngày, không raise blocker

✅ Dev nên:
  - Mention QA khi merge: "@qa-alice please test"
  - Hỏi PM ngay khi unclear: "@pm-bob AC này chưa rõ"
  - Raise blocker sớm: "Blocked by #88, waiting API"
```

---

### ❌ Anti-pattern 3: QA passive

```
❌ QA:
  - Đợi ai đó assign issue test
  - Issue unclear, test "theo cảm"
  - Tìm bug, không report ngay

✅ QA nên:
  - Proactive check "Testing" column
  - Hỏi PM nếu AC unclear
  - Report bug ngay khi tìm thấy
```

---

### ❌ Anti-pattern 4: Siloed work

```
❌ Team:
  - PM viết spec trong Google Doc riêng
  - Dev code, không đọc issue
  - QA test, không đọc PR

✅ Team nên:
  - Single source of truth = GitHub Issue
  - Dev đọc issue + AC trước khi code
  - QA đọc issue + PR trước khi test
```

---

## 🎯 Role-specific Checklist

### PM Checklist

```markdown
Tạo issue:
  - [ ] Title rõ ràng
  - [ ] AC đầy đủ, testable
  - [ ] Design/mockup attached
  - [ ] Priority + labels
  - [ ] Mention dev/QA nếu cần clarify

Sprint planning:
  - [ ] Present issues theo priority
  - [ ] Answer dev questions
  - [ ] Confirm sprint goal

Daily:
  - [ ] Check blocked issues
  - [ ] Clarify unclear issues
  - [ ] Review QA bugs, prioritize

Release:
  - [ ] Verify features trên staging
  - [ ] Close done issues
  - [ ] Write release notes
```

---

### Dev Checklist

```markdown
Pick issue:
  - [ ] Đọc issue + AC
  - [ ] Hỏi PM nếu unclear
  - [ ] Update status: In Progress
  - [ ] Estimate + comment ETA

Coding:
  - [ ] Reference issue in commits (#123)
  - [ ] Create PR, link issue (Closes #123)
  - [ ] Write tests
  - [ ] Request review

Merge:
  - [ ] Mention QA: "@qa-alice please test"
  - [ ] Update status: Review → Testing

Fix bugs:
  - [ ] Fix nhanh (< 1 ngày nếu critical)
  - [ ] Mention QA khi fix done
```

---

### QA Checklist

```markdown
Before test:
  - [ ] Đọc issue + AC
  - [ ] Đọc PR (understand changes)
  - [ ] Hỏi PM/Dev nếu unclear
  - [ ] Plan test cases

Testing:
  - [ ] Test theo AC
  - [ ] Test edge cases
  - [ ] Test regression
  - [ ] Screenshot bugs

Report result:
  - [ ] Comment: PASS or FAIL
  - [ ] FAIL → create bug issue (chi tiết)
  - [ ] PASS → update status: Done
  - [ ] Mention PM nếu ready for release
```

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu issue lifecycle: Backlog → Done
- [ ] Biết ai làm gì ở mỗi giai đoạn
- [ ] Biết cách communicate trong issue comments
- [ ] Biết cách link issues & PRs
- [ ] Biết khi nào mention người khác
- [ ] Hiểu anti-patterns phải tránh
- [ ] Có checklist cho role của mình

---

**🚀 Tiếp theo:**
- **PM** → [08-sprint-management.md](./08-sprint-management.md)
- **QA** → [10-practical-exercises.md](./10-practical-exercises.md)
- **All** → [12-anti-patterns.md](./12-anti-patterns.md)
