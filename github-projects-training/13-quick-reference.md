# 13 - Quick Reference Guide (Tài liệu tra cứu nhanh)

> **Mục tiêu**: Tra cứu nhanh templates, checklists, commands

**Đối tượng**: PM, QA, Dev (tất cả)

---

## 📝 Templates

### Template 1: Feature Issue

```markdown
## Title
[Feature] <Tên feature>

## Description
<Mô tả feature: là gì, tại sao cần>

## User Story
As a <user type>,
I want to <action>,
So that <benefit>.

## Acceptance Criteria
- [ ] <Criterion 1>
- [ ] <Criterion 2>
- [ ] <Criterion 3>

## Design / Mockup
<Link Figma>

## Technical Notes
<API, Database, Dependencies>

## Definition of Done
- [ ] Code completed & reviewed
- [ ] Unit tests written
- [ ] QA tested & passed
- [ ] Deployed to production

---
Labels: feature, <priority>, <team>
Milestone: <Sprint X>
```

---

### Template 2: Bug Issue

```markdown
## Title
[Bug] <Tóm tắt bug>

## Steps to Reproduce
1. <Bước 1>
2. <Bước 2>
3. <Bước 3>

## Expected Behavior
<Hành vi đúng>

## Actual Behavior
<Hành vi sai>

## Environment
- Browser: <Chrome 120>
- OS: <macOS 14>
- URL: <staging.myapp.com>

## Screenshots / Logs
<Attach>

## Severity
<Critical / High / Medium / Low>

---
Labels: bug, <severity>, <team>
Priority: <High>
```

---

### Template 3: Test Plan

```markdown
## Test Plan: <Feature Name>

### Test Scope
- <Scope 1>
- <Scope 2>

### Test Cases

#### TC-001: <Test case name>
- **Precondition**: <State trước khi test>
- **Steps**:
  1. <Step 1>
  2. <Step 2>
- **Expected**: <Kết quả mong đợi>

#### TC-002: <Edge case>
...

#### TC-003: <Error case>
...
```

---

## ✅ Checklists

### PM Checklist: Tạo Issue

```markdown
- [ ] Title rõ ràng, ngắn gọn
- [ ] Description đầy đủ (What, Why, Who)
- [ ] Acceptance Criteria testable
- [ ] Design/mockup attached (nếu cần)
- [ ] Technical notes (nếu có)
- [ ] Labels đúng (feature/bug/task, priority, team)
- [ ] Milestone/Sprint assigned
- [ ] Estimate (story points)
```

---

### QA Checklist: Test Issue

```markdown
Before test:
- [ ] Đọc issue + AC
- [ ] Đọc PR (understand changes)
- [ ] Plan test cases (happy, edge, error)
- [ ] Prepare test environment

During test:
- [ ] Test theo AC
- [ ] Test edge cases
- [ ] Test error handling
- [ ] Test regression
- [ ] Screenshot bugs

After test:
- [ ] Comment result: PASS/FAIL
- [ ] Create bug issues (nếu FAIL)
- [ ] Update issue status
- [ ] Mention PM/Dev
```

---

### Dev Checklist: Complete Issue

```markdown
Before coding:
- [ ] Đọc issue + AC
- [ ] Clarify unclear points với PM
- [ ] Update status: In Progress

During coding:
- [ ] Write code
- [ ] Write tests
- [ ] Commit với message: "ref #123: <description>"

Create PR:
- [ ] PR title: "Closes #123: <title>"
- [ ] PR body: "Closes #123"
- [ ] Link relevant docs
- [ ] Request review

After merge:
- [ ] Mention QA: "@qa-alice please test"
- [ ] Update status: Testing
- [ ] Monitor for bugs
```

---

### Sprint Planning Checklist

```markdown
Before meeting:
- [ ] PM: Prioritize backlog
- [ ] PM: Ensure top issues có AC rõ
- [ ] PM: Check team capacity
- [ ] PM: Draft sprint goal

During meeting:
- [ ] Review last sprint (15 min)
- [ ] Present sprint goal (10 min)
- [ ] Select & estimate issues (60 min)
- [ ] Confirm commitment (5 min)

After meeting:
- [ ] Update issues: Sprint field
- [ ] Move issues: Backlog → Todo
- [ ] Share sprint board
- [ ] Announce sprint goal
```

---

## 🎯 GitHub Projects Views

### View 1: Sprint Board (Kanban)

```
Filter: Sprint = "Current Sprint"
Layout: Board
Group by: Status
Sort: Priority DESC

Columns:
  Todo | In Progress | Review | Testing | Done
```

---

### View 2: Backlog Table

```
Filter: Status = Backlog
Layout: Table
Sort: Priority DESC, Created Date ASC

Columns:
  - Title
  - Priority
  - Estimation
  - Epic
  - Labels
```

---

### View 3: QA Testing Queue

```
Filter: Status = Testing
Layout: Table
Sort: Priority DESC

Columns:
  - Title
  - Priority
  - Assignee (Dev)
  - PR Link
  - Labels
```

---

### View 4: Bugs

```
Filter: Labels contains "bug"
Layout: Table
Group by: Severity
Sort: Created Date DESC

Columns:
  - Title
  - Severity
  - Status
  - Assignee
  - Created Date
```

---

## 🔑 GitHub Markdown Tips

### Linking Issues & PRs

```markdown
# Link issue
#123
fixes #123
closes #123
resolves #123

# Link PR
PR #456
pull request #456

# Link commit
abc1234 (commit SHA)

# Mention user
@username

# Mention team
@org/team-name
```

---

### Task Lists

```markdown
- [ ] Task 1
- [x] Task 2 (completed)
- [ ] Task 3
```

---

### Tables

```markdown
| Header 1 | Header 2 |
|----------|----------|
| Cell 1   | Cell 2   |
| Cell 3   | Cell 4   |
```

---

### Code Blocks

````markdown
```javascript
const foo = 'bar';
```

```python
def hello():
    print("Hello")
```
````

---

## 📊 Metrics Formulas

### Sprint Metrics

```
Velocity = Total story points completed

Completion Rate = (Issues done / Total issues) × 100%

Carry-over Rate = (Issues not done / Total issues) × 100%

Avg Cycle Time = Σ(Done date - Start date) / # issues
```

---

### Quality Metrics

```
Bug Escape Rate = (Bugs in prod / Total bugs) × 100%

QA Pass Rate = (Issues passed first time / Total tested) × 100%

Bug Reopen Rate = (Bugs reopened / Total bugs fixed) × 100%
```

---

## ⚡ Quick Commands

### GitHub CLI (gh)

```bash
# Create issue
gh issue create --title "Bug: Login fails" --body "..."

# List issues
gh issue list --label bug --state open

# Create PR
gh pr create --title "Fix login" --body "Closes #123"

# View PR
gh pr view 456

# Merge PR
gh pr merge 456 --squash

# Create project
gh project create --title "Sprint 15" --owner @me
```

---

### Git Tips cho Issue Tracking

```bash
# Commit reference issue
git commit -m "ref #123: Add login button"
git commit -m "fix #123: Fix login bug"

# Close issue via commit (khi merge to main)
git commit -m "closes #123: Complete login feature"

# View commits for issue
git log --grep="#123"
```

---

## 🎨 Status Examples

### Minimal Workflow (3 statuses)

```
Backlog → In Progress → Done
```

---

### Standard Workflow (5 statuses)

```
Backlog → Todo → In Progress → Review → Done
```

---

### Full Workflow (7 statuses)

```
Backlog → Todo → In Progress → Review → Testing → Done
                                  ↓
                              Blocked
```

---

## 📅 Sprint Timeline Example

```
Sprint 15: June 3 - June 16 (2 weeks)

Week 1:
  Mon: Sprint Planning (2h)
  Tue-Fri: Daily Standup (15 min) + Work
  Fri: Mid-sprint check-in

Week 2:
  Mon-Thu: Daily Standup + Work
  Fri: Sprint Review (1h) + Retro (1h)
```

---

## 🏷️ Label Convention

### Recommended Labels

```
Type:
  feature, bug, task, docs, test

Priority:
  critical, high-priority, medium-priority, low-priority

Team:
  team:frontend, team:backend, team:qa, team:design

Status (if not using columns):
  blocked, wontfix, duplicate, help-wanted

Size (optional):
  small, medium, large
```

---

## ⚠️ Common Mistakes & Fixes

| Mistake | Fix |
|---------|-----|
| Issue mơ hồ | Viết AC rõ ràng |
| PR không link issue | Add "Closes #123" in PR body |
| Không test regression | QA checklist regression |
| Overcommit sprint | Plan với buffer 20% |
| Bug report thiếu info | Dùng template bug |
| Micromanage | Trust + track board |

---

## 🔗 Useful Links

- [GitHub Projects Docs](https://docs.github.com/en/issues/planning-and-tracking-with-projects)
- [GitHub Flavored Markdown](https://github.github.com/gfm/)
- [GitHub CLI Manual](https://cli.github.com/manual/)
- [Agile Manifesto](https://agilemanifesto.org/)
- [INVEST Principle](https://en.wikipedia.org/wiki/INVEST_(mnemonic))

---

## 📞 Support & Feedback

**Có câu hỏi?**
- Tạo Discussion trong repo này
- Tag: `@pm-lead` hoặc `@qa-lead`

**Phát hiện lỗi trong tài liệu?**
- Tạo Issue: "Docs: <error description>"
- Hoặc tạo PR fix

**Góp ý cải thiện?**
- Tạo Issue: "Enhancement: <suggestion>"
- Team sẽ review & update

---

## ✅ Final Checklist

```markdown
Sau khi đọc toàn bộ tài liệu:
- [ ] Đã đọc file 01-09 (core concepts)
- [ ] Đã làm ít nhất 2 bài tập (file 10)
- [ ] Đã review anti-patterns (file 12)
- [ ] Đã bookmark file này (13) để tra cứu
- [ ] Sẵn sàng áp dụng vào công việc thật

Hành động tiếp theo:
- [ ] Apply vào 1 sprint thật
- [ ] Collect feedback từ team
- [ ] Review & adjust process
- [ ] Share learnings với team
```

---

**🎉 Chúc mừng! Bạn đã hoàn thành bộ tài liệu GitHub Projects Training!**

**🚀 Hành động tiếp theo:**
1. Áp dụng vào project thật
2. Thực hành 2-3 sprints
3. Review & cải thiện
4. Chia sẻ kiến thức với team

**💬 Feedback:**
Nếu tài liệu này hữu ích, hãy:
- ⭐ Star repo này
- 📣 Share với đồng nghiệp
- 💬 Góp ý để cải thiện

**Good luck! 🚀**
