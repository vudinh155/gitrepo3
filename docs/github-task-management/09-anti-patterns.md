# 09 - Anti-patterns (Lỗi thường gặp)

> **Mục tiêu**: Tránh các lỗi phổ biến khi dùng metrics

**Thời lượng**: 30 phút
**Đối tượng**: PO, HR, DM (tất cả)

---

## 🚫 Top 10 Anti-patterns

### 1. Chase Commit Count

#### ❌ Sai

```
Đánh giá:
  Dev A: 200 commits → Excellent
  Dev B: 50 commits → Poor

Hành động: Yêu cầu Dev B commit nhiều hơn
```

**Vấn đề:**
- Devs sẽ chia nhỏ commits vô lý
- Gaming the system
- Focus sai (số lượng thay vì chất lượng)

#### ✅ Đúng

```
Xem context:
  Dev A: 200 commits, nhưng code churn 4.0 (viết rồi xóa nhiều)
  Dev B: 50 commits, code churn 0.8 (stable, quality code)

→ Dev B tốt hơn
```

---

### 2. So sánh Cross-role

#### ❌ Sai

```
Frontend Dev: 30 issues/month → Good
Backend Dev: 15 issues/month → Poor

Action: Backend dev cần improve
```

**Vấn đề:**
- Frontend thường nhiều small UI tasks
- Backend ít tasks nhưng phức tạp hơn
- Không thể so sánh trực tiếp

#### ✅ Đúng

```
So sánh trong cùng role:
  Backend Dev A: 15 issues
  Backend Dev B: 12 issues
  Backend team avg: 14 issues

→ Dev A above avg, Dev B slightly below
```

---

### 3. Punishment bằng Metrics

#### ❌ Sai

```
Dev C có rework rate 40% (cao)

Action:
  - Public call-out trong meeting
  - Warning letter
  - Reduce bonus

Kết quả:
  - Dev C demoralized
  - Team scared metrics
  - Devs game metrics (approve PRs nhanh để tránh rework)
```

#### ✅ Đúng

```
Dev C có rework rate 40%

Action:
  - Private 1-on-1
  - Understand root cause (lack of training? Unclear requirements?)
  - Coaching plan
  - Pair programming with senior
  - Monitor improvement

Kết quả:
  - Dev C improves
  - Team trusts metrics for growth
```

---

### 4. Micromanagement Real-time

#### ❌ Sai

```
Manager check GitHub mỗi giờ:
  "Dev D chưa commit gì hôm nay, ping Slack"
  "PR #123 chưa merge, hỏi tại sao"

Kết quả:
  - Devs stressed
  - Commit junk code để "show activity"
  - Creativity killed
```

#### ✅ Đúng

```
Check metrics theo cycle:
  - Daily: Sprint progress (high-level)
  - Weekly: Blockers, risks
  - Sprint-end: Retrospective metrics
  - Monthly/Quarterly: Performance review

Trust team, focus on outcomes
```

---

### 5. Ignore Context

#### ❌ Sai

```
Dev E velocity giảm:
  Sprint 10: 40 points
  Sprint 11: 25 points

Action: Performance warning
```

**Missing context:**
- Sprint 11: Dev E onboard 2 new juniors (mentoring time)
- Sprint 11: Dev E handled critical production bug (not in sprint)
- Sprint 11: Dev E on-call rotation (interrupt)

#### ✅ Đúng

```
Ask for context:
  "I noticed velocity drop, what happened?"

Understand:
  - Mentoring: +value (team growth)
  - Production bug: +value (saved customers)
  - On-call: Expected interruption

→ Adjust evaluation accordingly
```

---

### 6. Short-term Optimization

#### ❌ Sai

```
Goal: Maximize sprint velocity

Devs optimize:
  - Skip code reviews (faster merge)
  - Skip tests (ship faster)
  - Take only easy tasks (more points)
  - Avoid refactoring (no immediate value)

Kết quả:
  - Short-term: Velocity ↑
  - Long-term: Tech debt ↑, Quality ↓, Velocity ↓
```

#### ✅ Đúng

```
Balanced goals:
  - Velocity (delivery)
  - Quality (rework rate, bugs)
  - Sustainability (tech debt, code health)
  - Collaboration (reviews, mentoring)

→ Long-term healthy team
```

---

### 7. One-size-fits-all Targets

#### ❌ Sai

```
Target for tất cả:
  - 40 story points/sprint
  - Rework rate < 20%
  - 10 reviews/sprint

Applied to:
  - Junior dev (6 months) ❌
  - Senior dev (5 years) ❌
  - Tech lead (mentoring 50% time) ❌
```

#### ✅ Đúng

```
Differentiated targets:

Junior:
  - 20 points/sprint (learning)
  - Rework < 40% (acceptable)
  - 5 reviews (observe & learn)

Senior:
  - 35 points/sprint (productive)
  - Rework < 15% (quality)
  - 15 reviews (help team)

Tech Lead:
  - 25 points/sprint (less coding)
  - Rework < 10% (role model)
  - 20 reviews + mentoring
```

---

### 8. Metrics Secrecy

#### ❌ Sai

```
HR track metrics bí mật:
  - Devs không biết mình được đo gì
  - Surprise khi performance review
  - Không có cơ hội improve

Kết quả:
  - Distrust
  - Anxiety
  - Gaming (guess metrics)
```

#### ✅ Đúng

```
Transparent metrics:
  - Share dashboard với team
  - Explain how metrics calculated
  - Regular feedback (not just annual review)
  - Give chance to improve

Kết quả:
  - Trust
  - Self-improvement
  - Alignment
```

---

### 9. Forget the Human

#### ❌ Sai

```
Dev F burnout:
  - Working 12h/day
  - High stress
  - Health issues

Manager:
  "But metrics good: 50 points/sprint, keep it up!"

Kết quả:
  - Dev F quits
  - Team morale ↓
```

#### ✅ Đúng

```
Notice signs:
  - Overtime hours
  - Stress signals
  - Health issues

Action:
  - 1-on-1: "Are you OK?"
  - Reduce workload
  - Support (counseling, time off)
  - Metrics secondary to wellbeing

→ Retain talent, healthy team
```

---

### 10. Metrics as Only Input

#### ❌ Sai

```
Promotion decision:
  - Dev G: 45 points/sprint → Promote
  - Dev H: 35 points/sprint → No promote

Ignore:
  - Dev H mentored 5 juniors
  - Dev H led architecture design
  - Dev H on-call hero (saved 3 incidents)
```

#### ✅ Đúng

```
Holistic evaluation:
  - Metrics (60%)
  - Tech lead review (30%)
  - Team feedback (10%)

Consider:
  - Leadership
  - Mentorship
  - Architecture contribution
  - On-call/support work

→ Fair promotion
```

---

## ⚠️ Warning Signs

### Team exhibiting anti-patterns:

```
🚩 Devs ask: "Will this count towards my metrics?"
🚩 PRs approved instantly (no real review)
🚩 Commit messages: "fix", "update", "change" (gaming commits)
🚩 Devs avoid complex tasks (only pick easy ones)
🚩 No one volunteers for on-call (not in metrics)
🚩 Team anxious about metrics discussion
🚩 Metrics gaming behavior (split 1 PR into 5 PRs)
```

**Action:**
- Review metrics system
- Re-communicate purpose (support, not punish)
- Adjust incentives
- Rebuild trust

---

## ✅ Principles to Follow

### 1. **Metrics for Support, not Surveillance**

```
✅ Use metrics to:
  - Identify who needs help
  - Recognize contributions
  - Optimize process
  - Coach & develop

❌ NOT to:
  - Micromanage
  - Punish
  - Create fear
  - Replace human judgment
```

---

### 2. **Context Matters**

```
Always ask:
  - What was the context?
  - What challenges did they face?
  - What invisible work did they do?
  - What's the full story?
```

---

### 3. **Human First**

```
People > Metrics

  - Wellbeing
  - Growth
  - Motivation
  - Team health

→ More important than hitting numbers
```

---

## ✅ Checklist sau khi đọc xong

```markdown
- [ ] Hiểu top 10 anti-patterns
- [ ] Biết warning signs của toxic metrics culture
- [ ] Commit áp dụng 3 principles
- [ ] Review metrics system hiện tại
- [ ] Đảm bảo team trust metrics
```

---

**🚀 Tiếp theo: [10-quick-reference.md](./10-quick-reference.md) - Tài liệu tra cứu nhanh**
