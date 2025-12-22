# 05 - Metrics and KPIs (Hệ thống chỉ số)

> **Mục tiêu**: Hiểu và sử dụng đúng metrics để đo lường

**Thời lượng**: 60 phút
**Đối tượng**: PO, HR, DM

---

## ⚠️ CẢNH BÁO QUAN TRỌNG

```
❌ KHÔNG chase số lượng:
   - Commit count
   - PR count
   - Lines of code

✅ FOCUS vào:
   - Impact (tác động)
   - Quality (chất lượng)
   - Collaboration (phối hợp)
```

---

## 📊 Metrics Categories

### 1️⃣ **Individual Metrics** (Cá nhân)

### A. **Productivity Metrics**

| Metric | Formula | Ý nghĩa | Target |
|--------|---------|---------|--------|
| **Issues Completed** | Count(issues closed by user) | Số task hoàn thành | Track trend |
| **Story Points Delivered** | Sum(points of completed issues) | Velocity cá nhân | Stable ±20% |
| **PR Merge Rate** | Merged PRs / Total PRs | Tỉ lệ PR được merge | > 80% |
| **Cycle Time** | Avg(Done date - Start date) | Thời gian hoàn thành task | < 5 days |

---

### B. **Quality Metrics**

| Metric | Formula | Ý nghĩa | Target |
|--------|---------|---------|--------|
| **Rework Rate** | PRs with requested changes / Total PRs | Tỉ lệ phải làm lại | < 30% |
| **Bug Rate** | Bugs created by user / Total issues | Tỉ lệ tạo bug | < 15% |
| **First-time Approval** | PRs approved first time / Total PRs | PR quality | > 70% |
| **Code Churn** | (Lines added + deleted) / Lines final | Code stability | < 2.0 |

---

### C. **Collaboration Metrics**

| Metric | Formula | Ý nghĩa | Target |
|--------|---------|---------|--------|
| **Reviews Given** | Count(reviews by user) | Đóng góp review | Min 10/sprint |
| **Review Quality** | Helpful comments / Total comments | Chất lượng review | Track |
| **Response Time** | Avg time to respond to mentions | Responsiveness | < 4h |
| **Pair Programming** | Hours pair coding | Collaboration | Track |

---

## 📈 Cách Tính Metrics

### Example 1: Issues Completed

**Cách lấy data:**

```
GitHub API:
GET /repos/:owner/:repo/issues?state=closed&assignee=username&since=2024-06-01

Hoặc GitHub Projects:
Filter: Status = Done AND Assignee = @username AND Closed >= Sprint start

Manual count:
Vào Profile → Contributions → Issues closed
```

**Interpretation:**

```
Dev Alice (Sprint 15):
  Issues completed: 8

Context needed:
  - Type: 5 features, 2 bugs, 1 task
  - Complexity: Avg 5 points/issue (40 points total)
  - Module: 6 core backend, 2 simple frontend

→ Good productivity với high complexity
```

---

### Example 2: Rework Rate

**Formula:**

```
Rework Rate = (PRs with "Request Changes") / (Total PRs created) × 100%
```

**Cách tính:**

```
Dev Bob (1 tháng):
  Total PRs: 20
  Approved first time: 14
  Request changes: 6

  Rework Rate = 6/20 × 100% = 30%

Benchmark:
  - < 20%: Excellent
  - 20-30%: Good
  - 30-50%: Needs improvement
  - > 50%: Serious quality issues
```

---

### Example 3: Review Contribution

**Cách tính:**

```
Reviews Given = Count(PRs reviewed by user)

Dev Carol (1 tháng):
  - PRs reviewed: 25
  - Comments: 120
  - Avg comments/review: 4.8
  - Helpful votes: 90 (75% helpful)

→ Active reviewer, high-quality feedback
```

---

## 🎯 Individual Performance Dashboard

### Template Dashboard (per person, per sprint)

```markdown
## Dev Alice - Sprint 15 Performance

### Productivity
- Issues completed: 8 (Target: 6-10)
- Story points: 42 (Velocity: stable)
- Cycle time: 3.5 days (Target: < 5)

### Quality
- Rework rate: 15% (Good)
- Bug rate: 10% (Good)
- First-time approval: 85% (Excellent)

### Collaboration
- Reviews given: 12 (Good)
- Comments: 45 (Helpful: 80%)
- Pair programming: 8 hours

### Notable
- Handled core auth module migration (high complexity)
- Mentored junior dev Bob
- Zero production bugs
```

---

## 📊 Team Metrics

### 2️⃣ **Team-level Metrics**

| Metric | Formula | Ý nghĩa | Target |
|--------|---------|---------|--------|
| **Sprint Velocity** | Sum(story points completed) | Team capacity | Stable |
| **Sprint Completion** | Issues done / Planned | Predictability | > 80% |
| **Lead Time** | Time from issue create → deployed | Efficiency | < 2 weeks |
| **Deployment Frequency** | Deploys per week | Agility | > 3/week |
| **Bug Escape Rate** | Prod bugs / Total bugs | Quality gate | < 10% |

---

## ⚠️ Metrics Anti-patterns

### ❌ Anti-pattern 1: Commit Count

```
BAD:
  Dev A: 200 commits
  Dev B: 50 commits
  Conclusion: Dev A productive 4x

REALITY:
  Dev A: Commits mỗi small change, nhiều fix commits
  Dev B: Commits atomic, meaningful

  Dev A: Churn rate 3.5 (viết rồi xóa nhiều)
  Dev B: Churn rate 0.8 (stable code)

→ Dev B quality tốt hơn
```

---

### ❌ Anti-pattern 2: Lines of Code

```
BAD:
  Dev A: 5000 lines added
  Dev B: 500 lines added
  Conclusion: Dev A productive 10x

REALITY:
  Dev A: Generated code, boilerplate
  Dev B: Core algorithm, high complexity

  Impact:
    Dev A: Low (replaceable code)
    Dev B: High (critical business logic)

→ Lines of code ≠ productivity
```

---

### ❌ Anti-pattern 3: So sánh Cross-role

```
BAD:
  Frontend Dev: 20 PRs/month
  Backend Dev: 10 PRs/month
  Conclusion: Frontend dev 2x productive

REALITY:
  Frontend: Nhiều UI changes, nhỏ, isolated
  Backend: Ít PRs nhưng complex, critical

→ KHÔNG thể so sánh trực tiếp
```

---

## ✅ Cách dùng Metrics ĐÚNG

### 1. **Metrics + Context**

```
✅ ĐÚNG:

Dev performance review:
  1. Metrics (60%):
     - 8 issues completed (40 points)
     - Rework rate: 15%
     - Reviews: 12

  2. Tech Lead input (30%):
     - Handled core module migration
     - Mentored 2 juniors
     - Quality: Excellent

  3. Team feedback (10%):
     - Helpful reviewer
     - Good collaborator

→ Holistic evaluation
```

---

### 2. **Trend > Absolute number**

```
✅ ĐÚNG:

Sprint 12: 30 points
Sprint 13: 32 points
Sprint 14: 31 points
Sprint 15: 29 points

→ Velocity STABLE (good)

❌ SAI:
Sprint 15: 29 points < Sprint 13: 32 points
→ Performance giảm (sai, vì fluctuation tự nhiên)
```

---

### 3. **Relative to team average**

```
✅ ĐÚNG:

Team average velocity: 35 points/sprint

Dev A: 40 points (Above average)
Dev B: 35 points (Average)
Dev C: 25 points (Below average)

But context:
  Dev C: Junior (6 months exp) + handled complex module
  → Actually good for level

→ Compare with context
```

---

## 📊 Metrics Collection Tools

### Option 1: GitHub Insights (Built-in)

```
Repository → Insights → Contributors

Metrics available:
  - Commits
  - Code frequency
  - Additions/Deletions
```

**Pros**: Free, built-in
**Cons**: Limited, no custom metrics

---

### Option 2: GitHub API + Custom Dashboard

```python
# Example: Get issues completed by user

import requests

def get_user_issues_completed(org, repo, username, since_date):
    url = f"https://api.github.com/repos/{org}/{repo}/issues"
    params = {
        "state": "closed",
        "assignee": username,
        "since": since_date
    }
    headers = {"Authorization": f"token {GITHUB_TOKEN}"}

    response = requests.get(url, params=params, headers=headers)
    issues = response.json()

    return len(issues)

# Usage
completed = get_user_issues_completed("myorg", "myrepo", "alice", "2024-06-01")
print(f"Alice completed {completed} issues")
```

---

### Option 3: Third-party Tools

```
- LinearB (metrics + insights)
- Waydev (engineering analytics)
- Pluralsight Flow (team performance)
- Jellyfish (engineering management platform)
```

---

## 📈 Monthly Report Template

```markdown
## Team Performance - June 2024

### Team Velocity
- Avg velocity: 180 points/sprint (stable)
- Sprint completion: 85% (target: 80%)
- Lead time: 8 days (target: < 10)

### Top Performers
1. Alice: 48 pts, 0 bugs, 15 reviews (Core contributor)
2. Bob: 42 pts, 1 bug, 12 reviews (Solid performer)
3. Carol: 38 pts, 0 bugs, 20 reviews (Reviewer champion)

### Areas of Improvement
- Bug escape rate: 12% (target: < 10%)
- Action: Strengthen QA process

### Team Highlights
- Launched Payment v2 (5 sprints)
- Zero production incidents
- 3 new features delivered
```

---

## ✅ Checklist sau khi đọc xong

```markdown
- [ ] Hiểu 3 loại metrics: Productivity, Quality, Collaboration
- [ ] Biết cách tính metrics cơ bản
- [ ] Hiểu anti-patterns: commit count, LOC, cross-role comparison
- [ ] Biết cách dùng metrics + context
- [ ] Biết tools để collect metrics
```

---

**🚀 Tiếp theo: [07-fair-evaluation-guidelines.md](./07-fair-evaluation-guidelines.md) - Đánh giá công bằng**
