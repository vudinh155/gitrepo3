# 11 - Advanced Techniques (Kỹ thuật nâng cao)

> **Mục tiêu**: Kỹ thuật nâng cao cho PM Lead, QA Lead

**Thời lượng**: 90 phút
**Đối tượng**: PM Lead, QA Lead, Tech Lead

---

## 🚀 Custom Fields nâng cao

### 1. Tạo Custom Fields hữu ích

```markdown
Recommended custom fields:

1. Epic (Text)
   - Nhóm issues theo epic
   - Ví dụ: "User Authentication", "Payment Module"

2. Estimation (Number)
   - Story points: 1, 2, 3, 5, 8, 13
   - Or hours estimate

3. Priority (Select)
   - Values: Critical, High, Medium, Low
   - Màu: Red, Orange, Yellow, Green

4. Team (Select)
   - Values: Frontend, Backend, QA, Design
   - Route issues to right team

5. QA Status (Select)
   - Values: Not Started, In Progress, Passed, Failed
   - Track QA progress riêng

6. Due Date (Date)
   - Deadline cho từng issue
   - Hiển thị trên Roadmap view
```

---

## 📊 Views chuyên biệt

### View 1: PM Planning View

```
Layout: Table
Filter: Status != Done
Group by: Epic
Sort: Priority DESC, Estimation ASC

Columns:
  - Title
  - Status
  - Priority
  - Estimation
  - Assignee
  - Sprint
  - Epic

Purpose: PM plan sprint, see big picture
```

---

### View 2: QA Dashboard

```
Layout: Board
Filter: Labels contains "bug" OR Status = "Testing"
Group by: QA Status

Columns:
  - Not Started
  - In Progress
  - Passed
  - Failed

Purpose: QA track testing progress
```

---

### View 3: Roadmap View

```
Layout: Roadmap
Filter: Priority = Critical OR Priority = High
X-axis: Due Date
Group by: Epic

Purpose: Show stakeholders timeline
```

---

## 🤖 Automation

### 1. Auto-update Status khi PR merge

```yaml
# .github/workflows/auto-status.yml
name: Auto Update Status

on:
  pull_request:
    types: [closed]

jobs:
  update:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v6
        with:
          script: |
            // Extract issue number from PR
            const pr = context.payload.pull_request;
            const match = pr.body.match(/closes #(\d+)/i);

            if (match) {
              const issueNumber = match[1];

              // Comment on issue
              await github.rest.issues.createComment({
                owner: context.repo.owner,
                repo: context.repo.repo,
                issue_number: issueNumber,
                body: '✅ PR merged. Ready for QA testing.\n\n@qa-team please test.'
              });
            }
```

---

### 2. Auto-assign Label dựa trên Title

```yaml
name: Auto Label

on:
  issues:
    types: [opened, edited]

jobs:
  label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v6
        with:
          script: |
            const title = context.payload.issue.title.toLowerCase();
            const labels = [];

            if (title.includes('[bug]')) labels.push('bug');
            if (title.includes('[feature]')) labels.push('feature');
            if (title.includes('urgent') || title.includes('critical')) {
              labels.push('high-priority');
            }

            if (labels.length > 0) {
              await github.rest.issues.addLabels({
                owner: context.repo.owner,
                repo: context.repo.repo,
                issue_number: context.payload.issue.number,
                labels: labels
              });
            }
```

---

## 📈 Advanced Metrics & Reporting

### 1. Velocity Tracking

```python
# Script to calculate velocity
import requests

def get_sprint_velocity(org, repo, sprint_number):
    # Get all issues in sprint
    url = f"https://api.github.com/repos/{org}/{repo}/issues"
    params = {
        "state": "closed",
        "labels": f"sprint-{sprint_number}"
    }

    response = requests.get(url, params=params)
    issues = response.json()

    # Sum story points
    total_points = 0
    for issue in issues:
        # Assuming "Estimation" custom field
        # (Need GraphQL API for custom fields)
        total_points += issue.get('estimation', 0)

    return total_points

# Usage
sprint_15_velocity = get_sprint_velocity("myorg", "myrepo", 15)
print(f"Sprint 15 velocity: {sprint_15_velocity} points")
```

---

### 2. Burndown Chart

```python
import matplotlib.pyplot as plt
from datetime import datetime, timedelta

# Data: Remaining points per day
sprint_days = list(range(1, 11))  # 10-day sprint
remaining_points = [56, 52, 48, 45, 38, 35, 30, 22, 15, 0]

# Ideal burndown
ideal = [56 - (56/10)*i for i in range(11)]

plt.plot(sprint_days, remaining_points, label='Actual', marker='o')
plt.plot(range(11), ideal, label='Ideal', linestyle='--')
plt.xlabel('Sprint Day')
plt.ylabel('Remaining Points')
plt.title('Sprint 15 Burndown Chart')
plt.legend()
plt.grid(True)
plt.show()
```

---

## 🔗 Integration với Tools khác

### 1. Slack Integration

```
GitHub App: Slack + GitHub
  - Install từ GitHub Marketplace
  - Connect repo với Slack channel
  - Setup notifications:
    - Issue created → #product channel
    - PR merged → #engineering channel
    - Release published → #general channel

Commands trong Slack:
  /github subscribe myorg/myrepo issues,pulls,releases
  /github subscribe myorg/myrepo +label:"bug"
```

---

### 2. Jira Migration Script

```python
# Migrate Jira issues to GitHub

import requests
from jira import JIRA

# Connect to Jira
jira = JIRA(server='https://mycompany.atlassian.net', basic_auth=('email', 'token'))

# Connect to GitHub
github_token = 'ghp_xxx'
github_api = 'https://api.github.com'
headers = {'Authorization': f'token {github_token}'}

# Get Jira issues
jira_issues = jira.search_issues('project=PROJ')

# Migrate each issue
for jira_issue in jira_issues:
    # Map Jira → GitHub
    github_issue = {
        'title': jira_issue.fields.summary,
        'body': jira_issue.fields.description,
        'labels': [label.name for label in jira_issue.fields.labels]
    }

    # Create GitHub issue
    response = requests.post(
        f'{github_api}/repos/myorg/myrepo/issues',
        headers=headers,
        json=github_issue
    )

    print(f"Migrated: {jira_issue.key} → GitHub #{response.json()['number']}")
```

---

## 🎨 Advanced Workflows

### 1. Multi-repo Project

```
Scenario: Project span nhiều repos (frontend, backend, mobile)

Setup:
1. Create Organization-level Project
2. Add issues từ nhiều repos:
   - myorg/frontend-repo
   - myorg/backend-repo
   - myorg/mobile-repo

3. Use custom field "Repository" để phân biệt
4. View tổng hợp tất cả repos trong 1 board
```

---

### 2. Epic Tracking với Project

```markdown
Epic Issue #500: [EPIC] Dark Mode

Child issues (cross-repo):
  - frontend-repo #101: Dark theme CSS
  - backend-repo #202: User preference API
  - mobile-repo #303: Dark mode toggle

Track progress:
  - View: Group by Epic
  - Filter: Epic = "Dark Mode"
  - See: 3 issues across 3 repos
```

---

## 📊 Advanced Reporting

### 1. Sprint Report Generator

```python
def generate_sprint_report(sprint_number):
    """Generate comprehensive sprint report"""

    report = f"""
# Sprint {sprint_number} Report

## Velocity
- Planned: 56 points
- Completed: 52 points (93%)
- Carry-over: 4 points (7%)

## Issues
- Total: 30 issues
- Completed: 28 issues (93%)
- Carried over: 2 issues (7%)

## Bugs
- Found: 5 bugs
- Fixed: 5 bugs
- Escaped to production: 0 bugs ✓

## Quality
- Test coverage: 92%
- QA pass rate: 85%
- Code review avg time: 3.2 hours

## Team
- Dev velocity: 26 points/dev
- QA throughput: 15 issues tested
- PM throughput: 8 issues created

## Retrospective Actions
1. Improve AC clarity (from retro)
2. Add regression test checklist
3. Mob programming 1x/week

## Next Sprint Focus
- Ship Dark Mode feature
- Reduce bug count to 0
    """

    return report
```

---

## ✅ Checklist sau khi đọc xong

- [ ] Hiểu custom fields nâng cao
- [ ] Biết tạo views chuyên biệt
- [ ] Biết automation cơ bản (GitHub Actions)
- [ ] Biết tích hợp Slack/Jira
- [ ] Biết track metrics & reporting

---

**🚀 Tiếp theo: [12-anti-patterns.md](./12-anti-patterns.md) - Anti-patterns & Best Practices**
