# 10 - Quick Reference (Tra cứu nhanh)

> **Mục tiêu**: Templates, formulas, checklists tra cứu nhanh

**Đối tượng**: PO, HR, DM (tất cả)

---

## 📝 Templates

### Issue Template

```markdown
## [Type][Module] Title

**Type**: Feature | Bug | Task | Refactor
**Module**: [Component name]
**Estimate**: [Story points]
**Owner**: @username
**Sprint**: Sprint X

## Description
[What & Why]

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

### Performance Review Template

```markdown
## [Name] - Q[X] Performance Review

### Metrics (60%)
- Issues: X (vs avg Y)
- Points: X (vs avg Y)
- Rework: X% (target < 20%)
- Bugs: X% (target < 15%)
**Score: X/100**

### Tech Lead Review (30%)
- Code quality: X/5
- Complexity: X/5
- Mentorship: X/5
**Score: X/100**

### Team Feedback (10%)
- Collaboration: X/5
- Helpfulness: X/5
**Score: X/100**

**Final IPS: X/100**
**Rating: [Exceptional/Exceeds/Meets/Needs Improvement]**
```

---

## 🧮 Formulas

### Individual Performance Score (IPS)

```
IPS = (GitHub Score × 0.6) + (Tech Lead Score × 0.3) + (Team Score × 0.1)

Scale: 0-100
  90-100: Exceptional
  80-89: Exceeds expectations
  70-79: Meets expectations
  60-69: Needs improvement
  < 60: Underperforming
```

---

### Key Metrics Formulas

```
Rework Rate = (PRs with requested changes / Total PRs) × 100%

Bug Rate = (Bugs created by user / Total issues) × 100%

Cycle Time = Average(Done date - Start date)

First-time Approval = (PRs approved first time / Total PRs) × 100%

Sprint Completion = (Issues done / Planned issues) × 100%

Velocity = Sum(story points completed)
```

---

## ✅ Checklists

### PO Checklist: Tạo Issue

```markdown
- [ ] Title: [Type][Module] Brief description
- [ ] Type assigned (feature/bug/task/refactor)
- [ ] Module specified
- [ ] Estimate (story points)
- [ ] Owner (1 person)
- [ ] AC rõ ràng
- [ ] DoD checklist
- [ ] Labels assigned
- [ ] Sprint/Milestone set
```

---

### HR Checklist: Performance Review

```markdown
Before review:
- [ ] Collect GitHub data (full period)
- [ ] Get Tech Lead input
- [ ] Collect peer feedback
- [ ] Consider context
- [ ] Compare within same role/level
- [ ] Check for biases
- [ ] Prepare specific examples
- [ ] Draft goals
- [ ] Schedule 1-on-1

During review:
- [ ] Share metrics transparently
- [ ] Discuss achievements
- [ ] Discuss growth areas
- [ ] Set clear goals
- [ ] Document action plan
```

---

### DM Checklist: Sprint Health

```markdown
Daily:
- [ ] Check sprint board
- [ ] Identify blocked issues
- [ ] Check cycle time outliers

Weekly:
- [ ] Review velocity trend
- [ ] Check rework rate
- [ ] Review bug rate
- [ ] Identify risks

Sprint end:
- [ ] Calculate completion rate
- [ ] Review retrospective
- [ ] Update team metrics
- [ ] Plan improvements
```

---

## 📊 Benchmarks

### Individual Metrics Benchmarks

| Metric | Good | Average | Needs Improvement |
|--------|------|---------|-------------------|
| Rework Rate | < 20% | 20-30% | > 30% |
| Bug Rate | < 10% | 10-20% | > 20% |
| First-time Approval | > 70% | 60-70% | < 60% |
| Cycle Time | < 3 days | 3-5 days | > 5 days |
| Reviews/Sprint | > 10 | 5-10 | < 5 |

---

### Team Metrics Benchmarks

| Metric | Good | Average | Needs Improvement |
|--------|------|---------|-------------------|
| Sprint Completion | > 80% | 70-80% | < 70% |
| Velocity (stable) | ±10% | ±20% | > 20% |
| Bug Escape Rate | < 5% | 5-10% | > 10% |
| Deployment Frequency | > 3/week | 1-3/week | < 1/week |
| Lead Time | < 1 week | 1-2 weeks | > 2 weeks |

---

## 🎯 Quick Decision Trees

### Should I intervene?

```
Issue stuck > 3 days?
  ├─ Yes → Check if blocked
  │   ├─ Blocked → Help unblock
  │   └─ Not blocked → 1-on-1 check-in
  └─ No → Monitor

Rework rate > 30%?
  ├─ Yes → Investigate root cause
  │   ├─ Skill gap → Training
  │   ├─ Unclear requirements → Improve AC
  │   └─ Rushing → Adjust workload
  └─ No → OK

Velocity dropping?
  ├─ Yes → Check context
  │   ├─ Mentoring → Expected
  │   ├─ On-call → Expected
  │   ├─ No clear reason → Investigate
  │   └─ Burnout signals → Urgent support
  └─ No → OK
```

---

## 📞 Support Resources

### GitHub API Examples

```python
# Get issues closed by user
GET /repos/:owner/:repo/issues?state=closed&assignee=username

# Get PRs by user
GET /repos/:owner/:repo/pulls?creator=username

# Get review comments
GET /repos/:owner/:repo/pulls/:number/reviews
```

---

### Useful GitHub CLI Commands

```bash
# List issues assigned to user
gh issue list --assignee username

# List PRs by user
gh pr list --author username

# View PR details
gh pr view 123

# List reviews by user
gh api /search/issues?q=reviewed-by:username+type:pr
```

---

## 🔗 External Tools

### Recommended Tools

```
Metrics & Analytics:
  - LinearB (engineering metrics)
  - Waydev (team analytics)
  - Pluralsight Flow (velocity tracking)

Dashboards:
  - Metabase (open-source BI)
  - Grafana (visualization)
  - Google Data Studio (free)

Automation:
  - GitHub Actions (CI/CD + automation)
  - Zapier (integrations)
```

---

## 📚 Further Reading

- [Google's DORA Metrics](https://dora.dev/)
- [Accelerate (Book)](https://itrevolution.com/product/accelerate/)
- [GitHub Engineering Blog](https://github.blog/category/engineering/)
- [SPACE Framework for Developer Productivity](https://queue.acm.org/detail.cfm?id=3454124)

---

## ✅ Final Checklist

```markdown
Implementation Checklist:
- [ ] Setup GitHub Projects with custom fields
- [ ] Define issue template
- [ ] Setup labels & modules
- [ ] Define metrics to track
- [ ] Create dashboards (PO, HR, DM views)
- [ ] Train team on standards
- [ ] Start collecting data
- [ ] First sprint retrospective
- [ ] First monthly review
- [ ] Iterate & improve
```

---

**🎉 Hoàn thành tài liệu! Ready to implement!**
