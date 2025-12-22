# 07 - Fair Evaluation Guidelines (Đánh giá công bằng)

> **Mục tiêu**: Đánh giá đóng góp công bằng, không máy móc

**Thời lượng**: 60 phút
**Đối tượng**: HR, Delivery Manager, Team Lead

---

## 🎯 Nguyên tắc vàng: Data + Context + Human Judgment

```
Formula:

Performance Evaluation =
  60% GitHub Data (objective)
+ 30% Tech Lead Review (expertise)
+ 10% Team Feedback (collaboration)

KHÔNG ĐƯỢC:
  ❌ 100% data (máy móc, thiếu context)
  ❌ 100% cảm giác (thiên vị, không khách quan)
```

---

## 📊 Framework đánh giá 360°

### Step 1: Collect GitHub Data (60%)

```markdown
Metrics tự động từ GitHub:
  ✓ Issues completed
  ✓ Story points delivered
  ✓ Rework rate
  ✓ Bug rate
  ✓ Reviews given
  ✓ Cycle time

Output: Quantitative score (0-100)
```

---

### Step 2: Tech Lead Review (30%)

```markdown
Tech Lead đánh giá:
  ✓ Code quality (review comments)
  ✓ Technical complexity handled
  ✓ Mentorship contribution
  ✓ Problem-solving ability
  ✓ Initiative & ownership

Output: Qualitative assessment (1-5 scale)
```

---

### Step 3: Team Feedback (10%)

```markdown
Peer review:
  ✓ Collaboration
  ✓ Communication
  ✓ Helpfulness
  ✓ Team player attitude

Output: Peer rating (1-5 scale)
```

---

## 🧮 Scoring System

### Individual Performance Score (IPS)

```
IPS = (GitHub Score × 0.6) + (Tech Lead Score × 0.3) + (Team Score × 0.1)

Scale: 0-100

  90-100: Exceptional
  80-89:  Exceeds expectations
  70-79:  Meets expectations
  60-69:  Needs improvement
  < 60:   Underperforming
```

---

### Example: Dev Alice

```markdown
## Alice - Q2 2024 Performance

### 1. GitHub Data (60%)
- Issues: 32 completed (Avg team: 28) → 114%
- Story points: 165 (Avg: 140) → 118%
- Rework rate: 12% (Target: < 20%) → Excellent
- Bug rate: 8% (Target: < 15%) → Good
- Reviews: 48 (Avg: 30) → 160%

GitHub Score: 92/100

### 2. Tech Lead Review (30%)
- Code quality: 5/5 (Clean, well-tested)
- Complexity: 5/5 (Core auth migration)
- Mentorship: 4/5 (Guided 2 juniors)
- Problem-solving: 5/5 (Solved critical OAuth issue)
- Initiative: 5/5 (Proposed & led refactoring)

Tech Lead Score: 96/100 (4.8/5 × 20)

### 3. Team Feedback (10%)
- Collaboration: 4.5/5
- Communication: 5/5
- Helpfulness: 5/5
- Team player: 5/5

Team Score: 95/100

---

**Final IPS:**
  (92 × 0.6) + (96 × 0.3) + (95 × 0.1)
= 55.2 + 28.8 + 9.5
= 93.5/100

**Rating: Exceptional**

**Justification:**
  - Consistent high performance
  - High-complexity work (core auth)
  - Strong collaboration & mentorship
  - Zero production bugs
```

---

## ⚖️ Fair Comparison Framework

### Rule 1: Compare within SAME role & level

```
✅ ĐÚNG:
  Senior Backend A vs Senior Backend B

❌ SAI:
  Frontend Junior vs Backend Senior
  (Different stack, different complexity)
```

---

### Rule 2: Adjust for complexity

```
Dev A: 50 points (10 simple UI tasks)
Dev B: 40 points (2 complex backend refactors)

❌ SAI: Dev A productive hơn (50 > 40)

✅ ĐÚNG:
  Dev A: Average complexity tasks
  Dev B: High complexity, high impact
  → Cần normalize by complexity
```

**Complexity Multiplier:**

```
Complexity factor:
  - Low (UI changes, simple CRUD): 1.0x
  - Medium (business logic, integration): 1.5x
  - High (core refactor, architecture): 2.0x
  - Critical (security, performance): 2.5x

Adjusted points:
  Dev A: 50 × 1.0 = 50
  Dev B: 40 × 2.0 = 80

→ Dev B higher impact
```

---

### Rule 3: Consider tenure & growth

```
Junior (< 1 year):
  - Lower expectations
  - Focus on growth rate
  - Mentorship needed

Mid (1-3 years):
  - Standard expectations
  - Independence

Senior (> 3 years):
  - High expectations
  - Mentorship expected
  - Leadership
```

**Example:**

```
Junior Dev Charlie (6 months):
  - 20 points/sprint
  - Rework rate: 40%
  - But: Growth from 10 → 20 points (100% growth!)

→ Rating: Meets expectations (for junior)
→ Focus: Continue learning

Senior Dev David (5 years):
  - 35 points/sprint
  - Rework rate: 15%
  - Mentored 3 juniors
  - Led architecture design

→ Rating: Exceeds expectations
```

---

## 🎯 Case Studies: Fair Evaluation

### Case 1: Dev with few commits but high impact

```
Dev Emma:
  - Commits: 80 (Low compared to team avg 150)
  - Issues: 6 (Low compared to avg 10)
  - Story points: 48 (High! Avg 35)

Context:
  - Worked on critical payment gateway migration
  - High complexity, high risk
  - Zero bugs, smooth launch
  - Saved company $50k/month

Evaluation:
  ❌ Naive: Low commits → low productivity
  ✅ Fair: High impact, critical work → Exceptional
```

---

### Case 2: Dev with many commits but low impact

```
Dev Frank:
  - Commits: 250 (High! Avg 150)
  - Issues: 15 (High! Avg 10)
  - Story points: 40 (Avg 35)

But:
  - Rework rate: 55% (Poor)
  - Bug rate: 30% (Poor)
  - Code churn: 4.5 (High churn)
  - Many commits fixing own bugs

Evaluation:
  ❌ Naive: High commits → high productivity
  ✅ Fair: Low quality, high rework → Needs improvement
```

---

### Case 3: QA with no code commits

```
QA Grace:
  - Commits: 5 (thêm test cases)
  - PRs: 3
  - BUT:
    - Issues reported: 45 bugs
    - Bug quality: 95% valid (not noise)
    - Prevented 5 critical production bugs
    - Test coverage: 85% → 92%

Evaluation:
  ❌ Naive: Low commits → low productivity
  ✅ Fair: High impact on quality → Exceeds expectations

→ KHÔNG thể dùng commit count cho QA!
```

---

## 📝 Evaluation Template

### Quarterly Review Template

```markdown
## Performance Review: [Name] - Q[X] 202X

### Role & Level
- Position: [Senior Backend Engineer]
- Tenure: [2 years]
- Team: [Payment Team]

### Quantitative Metrics (GitHub Data)

#### Productivity
- Issues completed: X (vs team avg Y)
- Story points: X (vs avg Y)
- Cycle time: X days (target < 5)

#### Quality
- Rework rate: X% (target < 20%)
- Bug rate: X% (target < 15%)
- First-time approval: X% (target > 70%)

#### Collaboration
- Reviews given: X (target > 10/sprint)
- Response time: X hours (target < 4h)

**GitHub Score: X/100**

---

### Qualitative Assessment (Tech Lead)

#### Strengths
- [Strength 1 with example]
- [Strength 2 with example]

#### Areas of Excellence
- [What they did exceptionally well]

#### Areas for Growth
- [Constructive feedback 1]
- [Constructive feedback 2]

**Tech Lead Score: X/100**

---

### Team Feedback (Peers)

**Collaboration:** X/5
**Communication:** X/5
**Helpfulness:** X/5

**Highlights from peers:**
- [Positive feedback quote 1]
- [Positive feedback quote 2]

**Team Score: X/100**

---

### Overall Performance Score

**IPS: X/100 (Rating: [Exceptional/Exceeds/Meets/Needs Improvement])**

### Key Achievements
1. [Major achievement 1]
2. [Major achievement 2]
3. [Major achievement 3]

### Development Goals (Next Quarter)
1. [Goal 1]
2. [Goal 2]
3. [Goal 3]

### Support Needed
- [What can manager/team provide]

---

**Manager Signature:** ___________
**Employee Signature:** ___________
**Date:** ___________
```

---

## ⚠️ Common Pitfalls

### ❌ Pitfall 1: Recency bias

```
BAD:
  Chỉ nhìn tháng cuối (tháng 3) mà quên 2 tháng đầu

GOOD:
  Xem trend cả quý:
    Month 1: 30 points
    Month 2: 35 points
    Month 3: 32 points
  → Consistent performance
```

---

### ❌ Pitfall 2: Halo effect

```
BAD:
  Dev A fix 1 critical bug → tất cả đều tốt

GOOD:
  Dev A:
    - Fixed critical bug (excellent)
    - But: High rework rate overall (needs improve)
    - And: Low collaboration (needs improve)
  → Mixed performance
```

---

### ❌ Pitfall 3: Comparison bias

```
BAD:
  Dev B ở team A (avg 40 pts/sprint)
  Dev C ở team B (avg 25 pts/sprint)

  Dev B: 42 points → Good
  Dev C: 28 points → BAD (so với Dev B)

GOOD:
  Compare within team context:
    Dev B: 42 vs team avg 40 → Above avg ✓
    Dev C: 28 vs team avg 25 → Above avg ✓
  → Both good relative to their teams
```

---

## 🎯 Action Plan sau khi đánh giá

### For High Performers (90-100)

```
Actions:
  ✓ Recognize publicly
  ✓ Reward (bonus, promotion consideration)
  ✓ Give stretch assignments
  ✓ Mentorship opportunities
  ✓ Retain (prevent turnover)
```

---

### For Solid Performers (70-89)

```
Actions:
  ✓ Positive feedback
  ✓ Identify growth areas
  ✓ Provide learning resources
  ✓ Set clear goals for next quarter
```

---

### For Underperformers (< 70)

```
Actions:
  ✓ 1-on-1 discussion
  ✓ Understand blockers
  ✓ Create improvement plan (30-60-90 days)
  ✓ Provide support (training, mentorship)
  ✓ Monitor progress closely
  ✓ Escalate if no improvement
```

---

## ✅ Checklist: Fair Evaluation

```markdown
Before finalizing evaluation:
  - [ ] Collected GitHub data for full period
  - [ ] Got Tech Lead input
  - [ ] Collected peer feedback
  - [ ] Considered context (complexity, role, tenure)
  - [ ] Compared within same level/role
  - [ ] Avoided recency bias
  - [ ] Checked for halo/horn effects
  - [ ] Prepared specific examples
  - [ ] Drafted development goals
  - [ ] Scheduled 1-on-1 for feedback delivery
```

---

## ✅ Checklist sau khi đọc xong

```markdown
- [ ] Hiểu formula: 60% data + 30% tech lead + 10% team
- [ ] Biết scoring system (IPS 0-100)
- [ ] Biết 3 rules: Same role, Complexity, Tenure
- [ ] Hiểu 3 case studies
- [ ] Biết evaluation template
- [ ] Biết pitfalls cần tránh
- [ ] Biết action plan cho từng tier
```

---

**🚀 Tiếp theo: [09-anti-patterns.md](./09-anti-patterns.md) - Lỗi thường gặp khi dùng metrics**
