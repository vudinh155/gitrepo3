# 08 - Sprint Management (Quản lý Sprint)

> **Mục tiêu**: Quản lý Sprint hiệu quả trên GitHub Projects

**Thời lượng**: 60 phút
**Đối tượng**: PM, QA Lead, Scrum Master

---

## 🎯 Sprint trong GitHub Projects

### Sprint là gì?

**Sprint** = Một khoảng thời gian cố định (thường 1-2 tuần) để team hoàn thành một tập hợp issues đã commit.

**Mục tiêu:**
- Focus: Team làm việc focused trên sprint goal
- Predictability: Stakeholder biết được gì sẽ ship khi nào
- Continuous improvement: Mỗi sprint học được gì để cải thiện

---

## 📅 Sprint Lifecycle

```
┌─────────────────┐
│ Sprint Planning │ (1-2 giờ: chọn issues, estimate, commit)
└────────┬────────┘
         ↓
┌─────────────────┐
│ Daily Standup   │ (15 phút mỗi ngày: sync progress, unblock)
└────────┬────────┘
         ↓
┌─────────────────┐
│ Sprint Review   │ (1 giờ: demo features done, collect feedback)
└────────┬────────┘
         ↓
┌─────────────────┐
│ Retrospective   │ (1 giờ: what went well, what to improve)
└────────┬────────┘
         ↓
       (Repeat)
```

---

## 🛠️ Setup Sprint trong GitHub Projects

### Option 1: Sử dụng Iteration Field (Recommended)

GitHub Projects có **Iteration field** native:

**Bước 1: Add Iteration field**

```
Project Settings → Fields → Add field
  Field type: Iteration
  Field name: Sprint
  Duration: 2 weeks
  Start date: Monday
```

**Bước 2: Tạo iterations**

```
Sprint 1: 2024-06-03 - 2024-06-16
Sprint 2: 2024-06-17 - 2024-06-30
Sprint 3: 2024-07-01 - 2024-07-14
...
```

**Bước 3: Assign issues vào sprint**

```
Issue #123 → Sprint = "Sprint 2"
Issue #124 → Sprint = "Sprint 2"
```

---

### Option 2: Sử dụng Milestone

```
Create Milestone:
  Title: Sprint 15
  Due date: 2024-06-16
  Description: Focus on "Save for Later" feature

Assign issues:
  Issue #123 → Milestone = Sprint 15
```

---

## 📋 Sprint Planning Meeting

### Trước meeting (PM chuẩn bị)

**PM Checklist (1 ngày trước):**

```markdown
- [ ] Review & prioritize backlog
- [ ] Đảm bảo top 20 issues có:
  - [ ] AC rõ ràng
  - [ ] Design/mockup (nếu cần)
  - [ ] Labels, priority
- [ ] Check team capacity:
  - [ ] Ai nghỉ phép?
  - [ ] Ai onboard mới (capacity thấp hơn)?
  - [ ] Holidays?
- [ ] Chuẩn bị sprint goal draft
- [ ] Send calendar invite + agenda
```

**Agenda gửi team:**

```
Sprint Planning - Sprint 15
Date: Mon, June 3, 2:00 PM - 3:30 PM

Agenda:
  1. Sprint 14 review (15 min)
  2. Sprint 15 goal (10 min)
  3. Issue selection & estimation (60 min)
  4. Commitment (5 min)

Pre-read:
  - Backlog: [link to GitHub Project Board]
  - Sprint goal draft: "Complete Save for Later feature + 3 P0 bugs"
```

---

### Trong meeting (90 phút)

#### **Part 1: Sprint Review (15 phút)**

```
PM:
  - Demo features done trong sprint 14
  - Metrics: Velocity, completion rate
  - What went well, what didn't

Team:
  - Feedback, lessons learned
```

**Ví dụ:**

```
Sprint 14 Results:
  ✅ Completed: 28/30 points (93%)
  ✅ Features: 5/6 done
  ❌ Carry-over: Issue #100 (blocked by design)

Lessons:
  ✓ AC rõ ràng hơn → dev hiểu ngay
  ✗ Issue #100 thiếu design → blocked 1 tuần
```

---

#### **Part 2: Sprint Goal (10 phút)**

**PM present sprint goal:**

```
Sprint 15 Goal:
  "Ship 'Save for Later' feature to production
   + Fix 3 critical bugs from production"

Why this goal:
  - Save for Later: Top user request (500 votes)
  - 3 bugs: Blocking checkout flow (revenue impact)

Success criteria:
  - Save for Later tested & deployed
  - 3 bugs fixed & verified
  - No new critical bugs introduced
```

---

#### **Part 3: Issue Selection & Estimation (60 phút)**

**PM:** Present issues theo thứ tự priority

```
Issue #123: [Feature] Save for Later
  Priority: P0
  AC: (đọc AC)
  Design: (show Figma)
  Questions?

Dev:
  - Estimate: 8 points (3 ngày)
  - Dependencies: Cần API từ backend

QA:
  - Estimate test: 1 ngày
  - AC rõ ràng ✓
```

**Estimate sử dụng Planning Poker:**

```
PM: "Issue #123, please estimate"
Dev vote: 5, 5, 8, 8
Discussion: "Tại sao 8?"
Consensus: 8 points (safe estimate)

Add to sprint ✓
```

**Repeat until:**
- Hết capacity, HOẶC
- Hết high-priority issues

**Capacity calculation:**

```
Team capacity:
  - 4 dev × 2 weeks × 10 points/dev/week = 80 points
  - Meetings, emails, etc: -20% = 64 points
  - Buffer (bugs, uncertainty): -10% = ~58 points

Selected issues: 56 points ✓ (fit capacity)
```

---

#### **Part 4: Commitment (5 phút)**

```
PM: "Team commit 56 points này nhé?"
Team: "OK ✓"
PM: "Awesome! Let's make it happen"

Actions:
  - Update all issues: Sprint = "Sprint 15"
  - Move issues: Backlog → Todo
  - Share sprint board link in Slack
```

---

## 📊 Sprint Tracking (Daily)

### Daily Standup (15 phút)

**Format:**

```
Team nhìn vào GitHub Project Board

Dev Alice:
  - Yesterday: Completed #123 → PR #124 merged
  - Today: Start #125
  - Blockers: None

Dev Bob:
  - Yesterday: Working on #126 → 70% done
  - Today: Finish #126, create PR
  - Blockers: Waiting design feedback for #127

QA Carol:
  - Yesterday: Tested #123 → PASS
  - Today: Test #126 when ready
  - Blockers: Issue #128 unclear AC → need PM clarify

PM:
  - Yesterday: Clarified #129, reviewed bugs
  - Today: Will unblock #127 (ping designer), clarify #128
  - Focus: Unblock team
```

**PM notes:**
- Issue #127: Blocked → escalate designer
- Issue #128: Unclear AC → clarify ngay sau standup

---

### Sprint Health Metrics (Check daily)

#### **View 1: Sprint Progress**

```
Filter: Sprint = "Sprint 15"
Group by: Status

Backlog  Todo  In Progress  Review  Testing  Done
   0      5        8          3        2      12

Progress: 12/30 issues done (40%)
Day 5/10 of sprint → on track ✓
```

#### **View 2: Blocked Issues**

```
Filter: Status = Blocked AND Sprint = "Sprint 15"

Issue #127: Blocked (waiting design) - 3 days
→ PM escalate designer
```

#### **View 3: Burndown (manual tracking)**

```
Day  | Remaining Points
-----|------------------
 1   | 56
 2   | 52
 3   | 48
 4   | 45
 5   | 38 (good progress)
 6   | 35
 7   | 30
 8   | 22
 9   | 15
10   | 0 (done!)
```

---

## 🚧 Sprint Management Best Practices

### ✅ Làm đúng

#### 1. **Protect sprint scope**

```
✅ Stakeholder request mid-sprint:
  PM: "Noted! I'll add to backlog, plan for Sprint 16"
  (KHÔNG thêm vào sprint hiện tại)

Exception: Critical production bug
  → Team discuss: swap với issue khác
  → Transparent with stakeholder
```

#### 2. **Unblock nhanh**

```
Issue blocked > 1 ngày:
  PM action:
    - Escalate designer/backend/stakeholder
    - Provide workaround
    - Swap với issue khác (nếu cần)

Goal: No issue blocked > 2 days
```

#### 3. **Transparent communication**

```
Sprint progress:
  - Daily: Update trong standup
  - Mid-sprint: Send update to stakeholders
    "Sprint 15: 50% done, on track ✓"
  - End sprint: Share metrics
```

#### 4. **Consistent velocity**

```
Track velocity:
  Sprint 12: 50 points
  Sprint 13: 52 points
  Sprint 14: 48 points
  Sprint 15: Plan 50 points (±10% is good)

Avoid:
  Sprint 12: 50 points
  Sprint 13: 80 points (overcommit → burnout)
  Sprint 14: 30 points (undercommit → waste capacity)
```

---

### ❌ Làm sai

#### 1. **Thay đổi scope giữa sprint**

```
❌ PM:
  Stakeholder yêu cầu → thêm issue vào sprint
  → Dev overwhelmed → miss sprint goal

✅ PM nên:
  Protect sprint scope
  Add to next sprint
```

#### 2. **Không track progress**

```
❌ PM:
  Không check board
  Cuối sprint mới biết miss target

✅ PM nên:
  Check board daily
  Catch risk sớm
  Adjust kịp thời
```

#### 3. **Carry-over quá nhiều**

```
❌ Team:
  Sprint 15: Carry-over 10/30 issues (33%)
  Sprint 16: Carry-over 12/30 issues (40%)
  → Sign of overcommit hoặc poor estimation

✅ Target:
  Carry-over < 20%
```

---

## 🎯 Sprint Review Meeting (1 giờ)

### Mục tiêu

- Demo features done
- Collect feedback từ stakeholders
- Celebrate wins

### Agenda

```
1. Sprint goal review (5 min)
2. Demo features (30 min)
3. Metrics (10 min)
4. Feedback & Q&A (15 min)
```

### Demo features

**PM/Dev:**

```
"Issue #123: Save for Later"

Demo:
  1. User add 3 items to cart
  2. Click "Save for Later" on item #1
  3. Item moves to "Saved Items" tab
  4. Logout → Login → Still see saved item ✓

(Live demo trên staging)

Stakeholder feedback:
  - Looks good ✓
  - Can we add notification when price drops?
  → PM: Good idea! Add to backlog
```

---

## 🔄 Retrospective Meeting (1 giờ)

### Mục tiêu

- Reflect: Gì tốt, gì cần improve
- Actionable: Commit cải thiện sprint sau

### Format: Start-Stop-Continue

```
📗 START (Bắt đầu làm gì?)
  - Dev: Review AC với PM trước khi estimate
  - QA: Test regression mỗi PR (không chỉ cuối sprint)

📕 STOP (Ngưng làm gì?)
  - PM: Ngưng thêm issues mid-sprint
  - Dev: Ngưng merge PR không notify QA

📘 CONTINUE (Tiếp tục làm gì?)
  - Daily standup focused (< 15 min)
  - AC rõ ràng → dev hiểu ngay
```

### Action items

```
Sprint 16 Actions:
  1. PM: Add checklist "Review AC with dev before planning"
  2. Dev: Add GitHub Action: auto-mention QA when PR merged
  3. Team: Try mob programming 1x/week for complex issues

Owner + Deadline:
  - Action 1: @pm-bob, by Sprint 16 planning
  - Action 2: @dev-alice, by Day 3 Sprint 16
  - Action 3: @team, try in Sprint 16
```

---

## 📈 Sprint Metrics

### Track mỗi sprint

| Metric | Cách đo | Target |
|--------|---------|--------|
| **Velocity** | Story points completed | Stable (±10%) |
| **Completion Rate** | % issues done | > 80% |
| **Carry-over Rate** | % issues carry to next sprint | < 20% |
| **Bug Escape Rate** | Bugs found in production | < 5% |
| **Cycle Time** | Avg days from Todo → Done | < 5 days |

### Ví dụ sprint report

```
Sprint 15 Metrics:

Velocity: 52/56 points (93%) ✓
Completion: 28/30 issues (93%) ✓
Carry-over: 2 issues (7%) ✓
Bugs: 1 bug escaped (3%) ✓
Cycle time: 4.2 days ✓

🎉 Great sprint! All targets met
```

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu sprint lifecycle
- [ ] Biết cách setup sprint trong GitHub Projects
- [ ] Biết cách run Sprint Planning meeting
- [ ] Biết cách track sprint progress daily
- [ ] Biết cách protect sprint scope
- [ ] Biết cách run Sprint Review & Retrospective
- [ ] Biết metrics cần track

---

**🚀 Tiếp theo: [09-release-management.md](./09-release-management.md) - Quản lý Release**
